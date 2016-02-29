```ruby
class Diagram
    class << self
      # Generates a new domain model based on all <tt>ActiveRecord::Base</tt>
      # subclasses, and creates a new diagram. Use the given options for both
      # the domain generation and the diagram generation.
      def create(options = {})
        new(Domain.generate(options), options).create
      end

      protected

      def setup(&block)
        callbacks[:setup] = block
      end

      def each_entity(&block)
        callbacks[:each_entity] = block
      end

      def each_relationship(&block)
        callbacks[:each_relationship] = block
      end

      def each_specialization(&block)
        callbacks[:each_specialization] = block
      end

      def save(&block)
        callbacks[:save] = block
      end

      private

      def callbacks
        @callbacks ||= Hash.new { proc {} }
      end
    end

    # The options that are used to create this diagram.
    attr_reader :options

    # The domain that this diagram represents.
    attr_reader :domain

    # Create a new diagram based on the given domain.
    def initialize(domain, options = {})
      @domain, @options = domain, RailsERD.options.merge(options)
    end

    # Generates and saves the diagram, returning the result of +save+.
    def create
      generate
      save
    end

    # Generates the diagram, but does not save the output. It is called
    # internally by Diagram#create.
    def generate
      instance_eval(&callbacks[:setup])

      filtered_entities.each do |entity|
        instance_exec entity, filtered_attributes(entity), &callbacks[:each_entity]
      end

      filtered_specializations.each do |specialization|
        instance_exec specialization, &callbacks[:each_specialization]
      end

      filtered_relationships.each do |relationship|
        instance_exec relationship, &callbacks[:each_relationship]
      end
    end

    def save
      instance_eval(&callbacks[:save])
    end

    private

    def callbacks
      @callbacks ||= self.class.send(:callbacks)
    end

    def filtered_entities
      @domain.entities.reject { |entity|
        options.exclude.present? && entity.model && [options.exclude].flatten.map(&:to_sym).include?(entity.name.to_sym) or
        options.only.present? && entity.model && ![options.only].flatten.map(&:to_sym).include?(entity.name.to_sym) or
        !options.inheritance && entity.specialized? or
        !options.polymorphism && entity.generalized? or
        !options.disconnected && entity.disconnected?
      }.compact.tap do |entities|
        raise "No entities found; create your models first!" if entities.empty?
      end
    end

    def filtered_relationships
      @domain.relationships.reject { |relationship|
        !options.indirect && relationship.indirect?
      }
    end

    def filtered_specializations
      @domain.specializations.reject { |specialization|
        !options.inheritance && specialization.inheritance? or
        !options.polymorphism && specialization.polymorphic?
      }
    end

    def filtered_attributes(entity)
      entity.attributes.reject { |attribute|
        # Select attributes that satisfy the conditions in the :attributes option.
        !options.attributes or entity.specialized? or
        [*options.attributes].none? { |type| attribute.send(:"#{type.to_s.chomp('s')}?") }
      }
    end

    def warn(message)
      puts "Warning: #{message}" if options.warn
    end
  end
end
```
