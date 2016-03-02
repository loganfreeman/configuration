layout
```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  before_filter :render_single_page

  private

  def ensure_authenticated
    if session[:user_id].blank?
      render :json => { authorized: false }
    end
  end

  def render_single_page
    render 'layouts/application' if request.format == Mime::HTML
  end

end
```
routes
```coffee
# defining all routes
angular.module('cafeTownsend').config [
  '$routeProvider'
  '$locationProvider'
  ($routeProvider, $locationProvider) ->
    $routeProvider
    .when '/login',
      templateUrl: '/assets/login.html',
      controller: 'LoginController'
      controllerAs: 'loginCtrl'

    .when '/employees',
      templateUrl: '/assets/employees.html'
      controller: 'EmployeesController'
      controllerAs: 'employeesCtrl'

    .when '/employees/new',
      templateUrl: '/assets/employee-create.html'
      controller: 'CreateEmployeeController'
      controllerAs: 'createEmployeeCtrl'

    .when '/employees/:id/edit',
      templateUrl: '/assets/employee-edit.html'
      controller: 'EditEmployeeController'
      controllerAs: 'editEmployeeCtrl'

    .otherwise
      redirectTo: '/login'

    # enabling html5Mode
    $locationProvider.html5Mode true
]
```
