layout
```ruby
<!DOCTYPE html>
<html>
<head>
  <title><%= @filename %></title>
  <%= stylesheet_link_tag    "browserlog/application", media: "all" %>
  <%= javascript_include_tag "browserlog/application" %>
  <%= csrf_meta_tags %>
</head>
<body data-theme="light">

<%= yield %>

  <button class="resume-scroll-btn hidden">Resume</button>

  <select class="theme-switch">
    <option value="dark">Dark</option>
    <option value="light">Light</option>
  </select>

</body>
</html>
```
