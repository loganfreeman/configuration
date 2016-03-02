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
viewstate
---html
<div class="main-view"
   ng-controller="MainController as mainCtrl"
   ng-class="{login:'main-view-loggedIn',
    employees:'main-view-employees',
    edit:'main-view-edit',
    create:'main-view-create'}[mainCtrl.viewState.current]">

  <header ng-controller="HeaderController as headerCtrl">
    <div
      ng-class="{'hide-header':!headerCtrl.authorized()}"
      class="header-container"
      ng-cloak
    >
      <p class="main-button" ng-click="headerCtrl.logout()">Logout</p>
      <p id="greetings">Hello {{headerCtrl.user.name}}</p>
    </div>
  </header>

  <div class="main-view-wrapper"
     ng-class="{login:'main-view-wrapper-loggedIn',
      employees:'main-view-wrapper-employees',
      edit:'main-view-wrapper-edit',
      create:'main-view-wrapper-create'}[mainCtrl.viewState.current]">
    <div ng-view
      class="main-view-container"
      ></div>
  </div>
</div>
```
