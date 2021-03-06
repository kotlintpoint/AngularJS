# AngularJS 

## ng-view 

- ngView is a directive that complements the $route service by including the rendered template of the current route into the main layout (index.html) file. Every time the current route changes, the included view changes with it according to the configuration of the $route service.
- Requires the ngRoute module to be installed.
- ngRoute is no longer a part of the base angular.js file, so you'll need to include the angular-route.js file after your the base angular javascript file.
```
<script src = "https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular-route.min.js"></script>
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
- It provides us method to keep data across the lifetime of the angular app
- It provides us method to communicate data across the controllers in a consistent way
- This is a singleton object and it gets instantiated only once per application
- It is used to organize and share data and functions across the application
- Two main execution characteristics of angular services are that they are singleton and lazy instantiated.
```
<script type="text/javascript">
app.service('Calculator',function(){
		this.square=function(a){
		return a*a;
	}
});

app.controller('MyCalcController',function($scope,Calculator){
	$scope.findSquare=function(){
		$scope.result=Calculator.square($scope.number);
	};
});
</script>
```
```
<body ng-app="MyApplication">	
	<div ng-controller="MyCalcController">
		Enter Number : <input type="text" ng-model="number"/><br/>
		&nbsp;&nbsp;&nbsp;<button ng-click="findSquare()">Square</button><br/>
		Result : <span>{{result}}</span>
	</div>
</body>
```
[Contact Manager Demo Using Service](https://github.com/kotlintpoint/AngularJS/blob/master/ContactManagerService.md)

# Scopes 

- The scope is the binding part between the HTML (view) and the JavaScript (controller).
- The scope is a JavaScript object with properties and methods, which are available for both the view and the controller.
- Then the scope is the Model.
```
<div ng-app="myApp" ng-controller="myCtrl">

<h1>{{username}}</h1>

</div>

<script>
var app = angular.module('myApp', []);

app.controller('myCtrl', function($scope) {
    $scope.username = "KotlinTpoint";
});
</script>
```
###### Another Example using Scope
```
<script>
angular.module('scopeExample', [])
.controller('MyController', ['$scope', function($scope) {
  $scope.username = 'World';

  $scope.sayHello = function() {
    $scope.greeting = 'Hello ' + $scope.username + '!';
  };
}]);
</script>
<body ng-app="scopeExample">
  <div ng-controller="MyController">
  Your name:
    <input type="text" ng-model="username">
    <button ng-click='sayHello()'>greet</button>
  <hr>
  {{greeting}}
</div>
</body>
```
# Controller

- In AngularJS, a Controller is defined by a JavaScript constructor function that is used to augment the AngularJS Scope.
- When a Controller is attached to the DOM via the ng-controller directive, AngularJS will instantiate a new Controller object, using the specified Controller's constructor function. 
- If the controller has been attached using the "controller as" syntax then the controller instance will be assigned to a property on the new scope.
- Use controllers to:
	- Set up the initial state of the $scope object.
	- Add behavior to the $scope object.

###### Setting up initial state of $scope object
```
var myApp = angular.module('myApp',[]);

myApp.controller('GreetingController', ['$scope', function($scope) {
  $scope.greeting = 'Hello World!';
}]);
......
<div ng-controller="GreetingController">
  {{ greeting }}
</div>
```
###### Another Example
```
<script type="text/javascript">
	var app=angular.module("MyApp",[]);
	app.controller("MyCtrl",function($scope){
		$scope.firstName='Ankit';
		$scope.lastName='Sodha';
		$scope.fullName=function(){
			return $scope.firstName+" "+$scope.lastName;
		}
	});
</script>
....
<body ng-app="MyApp" ng-controller="MyCtrl">
	<input type="text" ng-model="firstName"><br>
	<input type="text" ng-model="lastName"><br>
	<div>{{fullName()}}</div>
</body>	
```
# Directives in Angular JS
- At a high level, directives are markers on a DOM element (such as an attribute, element name, comment or CSS class) that tell AngularJS's HTML compiler ($compile) to attach a specified behavior to that DOM element (e.g. via event listeners), or even to transform the DOM element and its children.
- AngularJS comes with a set of these directives built-in, like ngBind, ngModel, and ngClass. Much like you create controllers and services, you can create your own directives for AngularJS to use. 
- When AngularJS bootstraps your application, the HTML compiler traverses the DOM matching directives against the DOM elements.
- [Angular Js Built-in directives](http://www.techstrikers.com/AngularJS/angularjs-built-in-directives.php)


# Filters 
- Selects a subset of items from array and returns it as a new array.
- Filters Provided by Angular JS
	- **currency** Format a number to a currency format.
	- **date** Format a date to a specified format.
	- **filter** Select a subset of items from an array.
	```
	<label>Any: <input ng-model="search.$"></label> <br>
	<label>Name only <input ng-model="search.name"></label><br>
	<label>Phone only <input ng-model="search.phone"></label><br>
	<label>Equality <input type="checkbox" ng-model="strict"></label><br>
	<table id="searchObjResults">
	  <tr><th>Name</th><th>Phone</th></tr>
	  <tr ng-repeat="friendObj in friends | filter:search:strict">
	    <td>{{friendObj.name}}</td>
	    <td>{{friendObj.phone}}</td>
	  </tr>
	</table>
	```
	- **json** Format an object to a JSON string.
	```
	<pre id="default-spacing">{{ {'name':'value'} | json }}</pre>
	<pre id="custom-spacing">{{ {'name':'value'} | json:4 }}</pre>
	```
	- **limitTo** Limits an array/string, into a specified number of elements/characters.
	```
	<script>
	  angular.module('limitToExample', [])
	    .controller('ExampleController', ['$scope', function($scope) {
	      $scope.numbers = [1,2,3,4,5,6,7,8,9];
	      $scope.letters = "abcdefghi";
	      $scope.longNumber = 2345432342;
	      $scope.numLimit = 3;
	      $scope.letterLimit = 3;
	      $scope.longNumberLimit = 3;
	    }]);
	</script>
	<div ng-controller="ExampleController">
	  <label>
	     Limit {{numbers}} to:
	     <input type="number" step="1" ng-model="numLimit">
	  </label>
	  <p>Output numbers: {{ numbers | limitTo:numLimit }}</p>
	  <label>
	     Limit {{letters}} to:
	     <input type="number" step="1" ng-model="letterLimit">
	  </label>
	  <p>Output letters: {{ letters | limitTo:letterLimit }}</p>
	  <label>
	     Limit {{longNumber}} to:
	     <input type="number" step="1" ng-model="longNumberLimit">
	  </label>
	  <p>Output long number: {{ longNumber | limitTo:longNumberLimit }}</p>
	</div>
	```
	- **lowercase** Format a string to lower case.
	```
	<input type="text" ng-model="firstName"><br>		
	<div>{{firstName | lowercase}}</div>
	```
	- **number** Format a number to a string.
	```
	<script>
	  angular.module('numberFilterExample', [])
	    .controller('ExampleController', ['$scope', function($scope) {
	      $scope.val = 1234.56789;
	    }]);
	</script>
	<div ng-controller="ExampleController">
	  <label>Enter number: <input ng-model='val'></label><br>
	  Default formatting: <span id='number-default'>{{val | number}}</span><br>
	  No fractions: <span>{{val | number:0}}</span><br>
	  Negative number: <span>{{-val | number:4}}</span>
	</div>
	```
	- **orderBy** Orders an array by an expression.
	```
	<div ng-init="friends = [{name:'John', phone:'555-1276'},
                         {name:'Mary', phone:'800-BIG-MARY'},
                         {name:'Mike', phone:'555-4321'},
                         {name:'Adam', phone:'555-5678'},
                         {name:'Julie', phone:'555-8765'},
                         {name:'Juliette', phone:'555-5678'}]"></div>
	<table id="searchTextResults">
	  <tr><th>Name</th><th>Phone</th></tr>
	  <tr ng-repeat="friend in friends | orderBy:'name'">
	    <td>{{friend.name}}</td>
	    <td>{{friend.phone}}</td>
	  </tr>
	</table>
	```
	- **uppercase** Format a string to upper case.
	```
	<input type="text" ng-model="firstName"><br>	
	<div>{{firstName | uppercase}}</div>	
	```
###### Other Examples 
```
<div ng-init="friends = [{name:'John', phone:'555-1276'},
                         {name:'Mary', phone:'800-BIG-MARY'},
                         {name:'Mike', phone:'555-4321'},
                         {name:'Adam', phone:'555-5678'},
                         {name:'Julie', phone:'555-8765'},
                         {name:'Juliette', phone:'555-5678'}]"></div>
<hr>
<label>Search: <input ng-model="searchText"></label>
<table id="searchTextResults">
  <tr><th>Name</th><th>Phone</th></tr>
  <tr ng-repeat="friend in friends | filter:searchText">
    <td>{{friend.name}}</td>
    <td>{{friend.phone}}</td>
  </tr>
</table>
<hr>
<label>Any: <input ng-model="search.$"></label> <br>
<label>Name only <input ng-model="search.name"></label><br>
<label>Phone only <input ng-model="search.phone"></label><br>
<label>Equality <input type="checkbox" ng-model="strict"></label><br>
<table id="searchObjResults">
  <tr><th>Name</th><th>Phone</th></tr>
  <tr ng-repeat="friendObj in friends | filter:search:strict">
    <td>{{friendObj.name}}</td>
    <td>{{friendObj.phone}}</td>
  </tr>
</table>
<hr>
<table border="1" width="100%">
  <tr>
    <th ng-click="orderByMe('name')">Name</th>
    <th ng-click="orderByMe('country')">Country</th>
  </tr>
  <tr ng-repeat="x in names | orderBy:myOrderBy">
    <td>{{x.name}}</td>
    <td>{{x.country}}</td>
  </tr>
</table>

</div>

<script>
angular.module('myApp', []).controller('namesCtrl', function($scope) {
  $scope.names = [
    {name:'Jani',country:'Norway'},
    {name:'Carl',country:'Sweden'},
    {name:'Margareth',country:'England'},
    {name:'Hege',country:'Norway'},
    {name:'Joe',country:'Denmark'},
    {name:'Gustav',country:'Sweden'},
    {name:'Birgit',country:'Denmark'},
    {name:'Mary',country:'England'},
    {name:'Kai',country:'Norway'}
  ];
  $scope.orderByMe = function(x) {
    $scope.myOrderBy = x;
  }
});
</script>
<hr><hr>

<ul ng-app="myApp" ng-controller="namesCtrl">
<li ng-repeat="x in names">
    {{x | myFormat}}
</li>
</ul>
<script>
var app = angular.module('myApp', []);
app.filter('myFormat', function() {
    return function(x) {
        var i, c, txt = "";
        for (i = 0; i < x.length; i++) {
            c = x[i];
            if (i % 2 == 0) {
                c = c.toUpperCase();
            }
            txt += c;
        }
        return txt;
    };
});
app.controller('namesCtrl', function($scope) {
    $scope.names = [
        'Sachin',
        'Sehwag',
        'Kohli',
        'Harbhajan',
        'Kapil',
        'Gambhir',
        'Zaheer',        
        ];
});
</script>
```
# Create New Custom Directives
- New directives are created by using the .directive function.
- To invoke the new directive, make an HTML element with the same tag name as the new directive.
- When naming a directive, you must use a camel case name, w3TestDirective, but when invoking it, you must use - separated name, w3-test-directive.
```
<student></student>

<script>
var app = angular.module("myApp", []);
app.directive("student", function() {
    return {
        template : "<h1>Made by a directive!</h1>"
    };
});
</script>
```
- You can invoke a directive by using:

    - Element name
    Ex. 
    ```
	<student></student>
    ```
    - Attribute
    Ex. 
    ```	
	<div student></div>
    ```
    - Class
     Ex. 
    ```	
	<div class="student"></div>
    ```
    - Comment
     Ex. 
    	```	
   	<!-- directive: w3-test-directive -->
	```
###### Restrictions
- You can restrict your directives to only be invoked by some of the methods.
- The legal restrict values are:

   - E for Element name
   - A for Attribute
   - C for Class
   - M for Comment
```
var app = angular.module("myApp", []);
app.directive("w3TestDirective", function() {
    return {
        restrict : "A",
        template : "<h1>Made by a directive!</h1>"
    };
});
```
- By default the value is EA, meaning that both Element names and attribute names can invoke the directive.
- $compile can match directives based on element names (E), attributes (A), class names (C), and comments (M).
- Best Practice: Prefer using directives via tag name and attributes over comment and class names. Doing so generally makes it easier to determine what directives a given element matches. 
```
angular.module('docsSimpleDirective', [])
.controller('Controller', ['$scope', function($scope) {
  $scope.customer = {
    name: 'Naomi',
    address: '1600 Amphitheatre'
  };
}])
.directive('myCustomer', function() {
  return {
    template: 'Name: {{customer.name}} Address: {{customer.address}}'
  };
});
```
- templateUrl function
```
angular.module('docsTemplateUrlDirective', [])
.controller('Controller', ['$scope', function($scope) {
  $scope.customer = {
    name: 'Naomi',
    address: '1600 Amphitheatre'
  };
}])
.directive('myCustomer', function() {
  return {
    templateUrl: function(elem, attr) {
      return 'customer-' + attr.type + '.html';
    }
  };
});


<div ng-controller="Controller">
  <div my-customer type="name"></div>
  <div my-customer type="address"></div>
</div>



```


