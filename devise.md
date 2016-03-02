links
---
```erb
<div class="devise_links">
  <%- if controller_name != 'sessions' %>
    <%= link_to "Sign in", new_session_path(resource_name) %><br />
  <% end -%>

  <%- if devise_mapping.registerable? && controller_name != 'registrations' %>
    <%= link_to "Sign up", new_registration_path(resource_name) %><br />
  <% end -%>

  <%- if devise_mapping.recoverable? && controller_name != 'passwords' %>
    <%= link_to "Forgot your password?", new_password_path(resource_name) %><br />
  <% end -%>

  <%- if devise_mapping.confirmable? && controller_name != 'confirmations' %>
    <%= link_to "Didn't receive confirmation instructions?", new_confirmation_path(resource_name) %><br />
  <% end -%>

  <%- if devise_mapping.lockable? && resource_class.unlock_strategy_enabled?(:email) && controller_name != 'unlocks' %>
    <%= link_to "Didn't receive unlock instructions?", new_unlock_path(resource_name) %><br />
  <% end -%>

  <%- if devise_mapping.omniauthable? %>
    <%- resource_class.omniauth_providers.each do |provider| %>
      <%= link_to "Sign in with #{provider.to_s.titleize}", omniauth_authorize_path(resource_name, provider) %><br />
    <% end -%>
  <% end -%>
</div>
```
session controller
---
```ruby
class Api::SessionsController < Devise::SessionsController
  before_action :warden_authenticate

  def create
    sign_in(resource_name, resource)
    resource.reset_authentication_token!
    render json: {auth_token: resource.authentication_token}
  end

  def destroy
    sign_out(resource_name)
    resource.clear_authentication_token!
    render nothing: true
  end

  private

  def warden_authenticate
    self.resource = warden.authenticate!(auth_options)
  end
end
```