# AngularJS 

## ng-view 

- ngView is a directive that complements the $route service by including the rendered template of the current route into the main layout (index.html) file. Every time the current route changes, the included view changes with it according to the configuration of the $route service.
- Requires the ngRoute module to be installed.
- ngRoute is no longer a part of the base angular.js file, so you'll need to include the angular-route.js file after your the base angular javascript file.
```
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.js"></script>
```
## $routeProvider

-Used for configuring routes.

- We can configure a route by using the “when” function of the $routeProvider. We need to first specify the route, then in a second parameter provide an object with a templateUrl property and a controller property.

###### mainController.js
```
var app=angular.module("MyApplication",['ngRoute']);

app.config(['$routeProvider', function ($routeProvider){
	
	$routeProvider
	.when('/add',
    {
      templateUrl:'views/add.html',
      controller:'addController'
    }
  )
	.when('/list',
    {
      templateUrl:'views/list.html',
      controller:'listController'
    }
  )
	.when('/edit',
    {
      templateUrl:'views/edit.html',
      controller:'editController'
    }
  )
	.otherwise({
		redirectTo:'/add'
	});	
}]);

app.controller('addController',function($scope){
	$scope.message="This page will be used to display add data form";
});

app.controller('listController',function($scope){
	$scope.message="This page will be used to display list data form";
});
app.controller('editController',function($scope){
	$scope.message="This page will be used to display edit data form";
});
```
- Now we need to create a template for the route. We’ll create the "add.html", "list.html" and "edit.html" file with a binding for the message within an h1 tag:
```
<h1>Add Page</h1>
```
```
<h1>Edit Page</h1>
```
```
<h1>List Page</h1>
```
## ng-template

- Load the content of a <script> element into $templateCache, so that the template can be used by ngInclude, ngView, or directives. 
- The type of the <script> element must be specified as text/ng-template, and a cache name for the template must be assigned through the element's id, which can then be used as a directive's templateUrl.

###### index.html
```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<!-- <script type="text/javascript" src="Scripts/angular.min.js"></script>
	<script type="text/javascript" src="Scripts/angular-route.min.js"></script>  -->
	<script src = "https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
    <script src = "https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular-route.min.js"></script>
	<script type="text/javascript" src="controller/mainController.js"></script>
</head>
<body ng-app="MyApplication">
<table>
	<tr>
		<td><a href="#add">Add View</a></td>
		<td><a href="#list">List View</a></td>
		<td><a href="#edit">Edit View</a></td>
	</tr>		
</table>
<div ng-view></div>
<script type="text/ng-template" id="add.html">
	<h2>Add</h2>
	{{message}}
</script>
<script type="text/ng-template" id="list.html">
	<h2>List</h2>
	{{message}}
</script>
<script type="text/ng-template" id="edit.html">
	<h2>Edit</h2>
	{{message}}
</script>
</body>
</html>
```
## Dependency Injection
- Dependency Injection (DI) is a software design pattern that deals with how components get hold of their dependencies.
- The AngularJS injector subsystem is in charge of creating components, resolving their dependencies, and providing them to other components as requested.

###### Using Dependency Injection
- Components such as services, directives, filters, and animations are defined by an injectable factory method or constructor function. These components can be injected with "service" and "value" components as dependencies.
- Controllers are defined by a constructor function, which can be injected with any of the "service" and "value" components as dependencies, but they can also be provided with special dependencies.

###### Understanding Value Recipe

- Let's say that we want to have a very simple service called "clientId" that provides a string representing an authentication id used for some remote API. You would define it like this:
```
var myApp = angular.module('myApp', []);
myApp.value('clientId', 'a12345654321x');
```
- Notice how we created an AngularJS module called myApp, and specified that this module definition contains a "recipe" for constructing the clientId service, which is a simple string in this case.
- And this is how you would display it via AngularJS's data-binding:
```
myApp.controller('DemoController', ['clientId', function DemoController(clientId) {
  this.clientId = clientId;
}]);
```
```
<html ng-app="myApp">
  <body ng-controller="DemoController as demo">
    Client ID: {{demo.clientId}}
  </body>
</html>
```
###### Understanding Factory Recipe
- The Value recipe is very simple to write, but lacks some important features we often need when creating services. Let's now look at the Value recipe's more powerful sibling, the Factory. The Factory recipe adds the following abilities:

  - ability to use other services (have dependencies)
  - service initialization
  - delayed/lazy initialization

- The Factory recipe constructs a new service using a function with zero or more arguments (these are dependencies on other services). The return value of this function is the service instance created by this recipe.
- Note: All services in AngularJS are singletons. That means that the injector uses each recipe at most once to create the object. The injector then caches the reference for all future needs.
- Since a Factory is a more powerful version of the Value recipe, the same service can be constructed with it. Using our previous clientId Value recipe example, we can rewrite it as a Factory recipe like this:
```
myApp.factory('clientId', function clientIdFactory() {
  return 'a12345654321x';
});
```
- But given that the token is just a string literal, sticking with the Value recipe is still more appropriate as it makes the code easier to follow.
- Let's say, however, that we would also like to create a service that computes a token used for authentication against a remote API. This token will be called apiToken and will be computed based on the clientId value and a secret stored in the browser's local storage:
```
myApp.factory('apiToken', ['clientId', function apiTokenFactory(clientId) {
  var encrypt = function(data1, data2) {
    // NSA-proof encryption algorithm:
    return (data1 + ':' + data2).toUpperCase();
  };

  var secret = window.localStorage.getItem('myApp.secret');
  var apiToken = encrypt(clientId, secret);

  return apiToken;
}]);
```
> Best Practice: name the factory functions as <serviceId>Factory (e.g., apiTokenFactory). While this naming convention is not required, it helps when navigating the codebase or looking at stack traces in the debugger.
- Just like with the Value recipe, the Factory recipe can create a service of any type, whether it be a primitive, object literal, function, or even an instance of a custom type.

###### Service Recipe

- The Service recipe produces a service just like the Value or Factory recipes, but it does so by invoking a constructor with the new operator. The constructor can take zero or more arguments, which represent dependencies needed by the instance of this type.
- Note: Service recipes follow a design pattern called constructor injection.

###### Provider Recipe

