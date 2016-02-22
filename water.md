```ruby
module Spring
  module Watcher
    # A user of a watcher can use IO.select to wait for changes:
    #
    #   watcher = MyWatcher.new(root, latency)
    #   IO.select([watcher]) # watcher is running in background
    #   watcher.stale? # => true
    class Abstract
      include Mutex_m

      attr_reader :files, :directories, :root, :latency

      def initialize(root, latency)
        super()

        @root        = File.realpath(root)
        @latency     = latency
        @files       = Set.new
        @directories = Set.new
        @stale       = false
        @listeners   = []
      end

      def add(*items)
        items = items.flatten.map do |item|
          item = Pathname.new(item)

          if item.relative?
            Pathname.new("#{root}/#{item}")
          else
            item
          end
        end

        items = items.select(&:exist?)

        synchronize {
          items.each do |item|
            if item.directory?
              directories << item.realpath.to_s
            else
              files << item.realpath.to_s
            end
          end

          subjects_changed
        }
      end

      def stale?
        @stale
      end

      def on_stale(&block)
        @listeners << block
      end

      def mark_stale
        return if stale?
        @stale = true
        @listeners.each(&:call)
      end

      def restart
        stop
        start
      end

      def start
        raise NotImplementedError
      end

      def stop
        raise NotImplementedError
      end

      def subjects_changed
        raise NotImplementedError
      end
    end
  end
end
```


```ruby
module Spring
  module Watcher
    class Polling < Abstract
      attr_reader :mtime

      def initialize(root, latency)
        super
        @mtime  = 0
        @poller = nil
      end

      def check_stale
        synchronize { mark_stale if mtime < compute_mtime }
      end

      def add(*)
        check_stale if @poller
        super
      end

      def start
        unless @poller
          @poller = Thread.new {
            Thread.current.abort_on_exception = true

            loop do
              Kernel.sleep latency
              check_stale
            end
          }
        end
      end

      def stop
        if @poller
          @poller.kill
          @poller = nil
        end
      end

      def subjects_changed
        @mtime = compute_mtime
      end

      private

      def compute_mtime
        expanded_files.map { |f| File.mtime(f).to_f }.max || 0
      rescue Errno::ENOENT
        # if a file does no longer exist, the watcher is always stale.
        Float::MAX
      end

      def expanded_files
        files + Dir["{#{directories.map { |d| "#{d}/**/*" }.join(",")}}"]
      end
    end
  end
end
```
