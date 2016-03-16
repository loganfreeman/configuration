### controller
```ruby
class CalendarsController < ApplicationController
  menu_item :calendar
  before_filter :find_optional_project

  rescue_from Query::StatementInvalid, :with => :query_statement_invalid

  helper :issues
  helper :projects
  helper :queries
  include QueriesHelper
  helper :sort
  include SortHelper

  def show
    if params[:year] and params[:year].to_i > 1900
      @year = params[:year].to_i
      if params[:month] and params[:month].to_i > 0 and params[:month].to_i < 13
        @month = params[:month].to_i
      end
    end
    @year ||= Date.today.year
    @month ||= Date.today.month

    @calendar = Redmine::Helpers::Calendar.new(Date.civil(@year, @month, 1), current_language, :month)
    retrieve_query
    @query.group_by = nil
    if @query.valid?
      events = []
      events += @query.issues(:include => [:tracker, :assigned_to, :priority],
                              :conditions => ["((start_date BETWEEN ? AND ?) OR (due_date BETWEEN ? AND ?))", @calendar.startdt, @calendar.enddt, @calendar.startdt, @calendar.enddt]
                              )
      events += @query.versions(:conditions => ["effective_date BETWEEN ? AND ?", @calendar.startdt, @calendar.enddt])

      @calendar.events = events
    end

    render :action => 'show', :layout => false if request.xhr?
  end
end
```

### view
```ruby
<table class="cal">
<thead>
<tr><th scope="col" title="<%= l(:label_week) %>" class="week-number"></th><% 7.times do |i| %><th scope="col"><%= day_name( (calendar.first_wday+i)%7 ) %></th><% end %></tr>
</thead>
<tbody>
<tr>
<% day = calendar.startdt
while day <= calendar.enddt %>
<%= ("<td class='week-number' title='#{ l(:label_week) }'>#{(day+(11-day.cwday)%7).cweek}</td>".html_safe) if day.cwday == calendar.first_wday %>
<td class="<%= day.month==calendar.month ? 'even' : 'odd' %><%= ' today' if Date.today == day %>">
<p class="day-num"><%= day.day %></p>
<% calendar.events_on(day).each do |i| %>
  <% if i.is_a? Issue %>
  <div class="<%= i.css_classes %> <%= 'starting' if day == i.start_date %> <%= 'ending' if day == i.due_date %> tooltip">
  <%= "#{i.project} -" unless @project && @project == i.project %>
  <%= link_to_issue i, :truncate => 30 %>
  <span class="tip"><%= render_issue_tooltip i %></span>
  </div>
  <% else %>
  <span class="icon icon-package">
    <%= "#{i.project} -" unless @project && @project == i.project %>
    <%= link_to_version i%>
  </span>
  <% end %>
<% end %>
</td>
<%= '</tr><tr>'.html_safe if day.cwday==calendar.last_wday and day!=calendar.enddt %>
<% day = day + 1
end %>
</tr>
</tbody>
</table>
```
### navigation
```ruby
<%= form_tag({:controller => 'calendars', :action => 'show', :project_id => @project},
             :method => :get, :id => 'query_form') do %>
<%= hidden_field_tag 'set_filter', '1' %>
<fieldset id="filters" class="collapsible <%= @query.new_record? ? "" : "collapsed" %>">
  <legend onclick="toggleFieldset(this);"><%= l(:label_filter_plural) %></legend>
  <div style="<%= @query.new_record? ? "" : "display: none;" %>">
    <%= render :partial => 'queries/filters', :locals => {:query => @query} %>
  </div>
</fieldset>

<p style="float:right;">
  <%= link_to_previous_month(@year, @month, :accesskey => accesskey(:previous)) %> | <%= link_to_next_month(@year, @month, :accesskey => accesskey(:next)) %>
</p>

<p class="buttons">
<%= label_tag('month', l(:label_month)) %>
<%= select_month(@month, :prefix => "month", :discard_type => true) %>
<%= label_tag('year', l(:label_year)) %>
<%= select_year(@year, :prefix => "year", :discard_type => true) %>

<%= link_to_function l(:button_apply), '$("#query_form").submit()', :class => 'icon icon-checked' %>
<%= link_to l(:button_clear), { :project_id => @project, :set_filter => 1 }, :class => 'icon icon-reload' %>
</p>
<% end %>
```
