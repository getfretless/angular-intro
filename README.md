# Angular Introduction

[AngularJS](https://angularjs.org) is a Google-created web framework that relies heavily on javascript for interactions, however tries very hard to draw sharp lines about how to organize and architect your application. It aims to keep view-related logic in the HTML layer, using special directives to bind behavior with data, and routing so you can build both small and large applications with it, and still maintain a reasonable structure.

Here's how you might update that `<span>` in realtime with jQuery:

```html
<html>
  <head>
    <script src="http://code.jquery.com/jquery.min.js"></script>
  </head>
  <body>
    <label for="name">Name:</label><br>
    <input id="textInput" type="text"><br>
    Hello <span id="nameDiv"></span>!

    <script>
      $('#textInput').on('keyup', function(){
        $('#nameDiv').html($('#textInput').val());
      });
    </script>
  </body>
</html>
```

Here's the same thing with angular:

```html
<html ng-app>
  <head>
    <script src="http://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.3/angular.min.js"></script>
  </head>
  <body>
    <label for="name">Name:</label><br>
    <input type="text" name="name" ng-model="name"><br>
    Hello <span id="nameDiv">{{ name }}</span>
  </body>
</html>
```

And since we only needed the `<span>` to insert the name, we can get rid of that entirely.

## Templating

Notice the `{{ name }}` construct, this is a [moustache](http://mustache.github.io/)-like template language, that internally, angular tracks and binds to the `ng-model`. You don't have to use spaces, but I will, because I find it easier to read. This will let us get rid of markup that was previously only necessary so we could update stuff inside it.

## 2-way data binding

So the "name" input, is bound to a model (that we can't really see), and whenever that input is updated, the model gets updated to match, and whenever that model gets updated, the moustache template variable also gets updated, and replaced.

The moustache values essentially register listeners to the `ng-model` input, to update whenever that gets changed.

## Module

The fun really starts when we want to store & load objects, arrays, and strings. We can define `$scope`s, or values that belong to the scope of an Angular Controller.
In Angular, the concept of the "Controller", is it is the thing that keeps track of a part of your web page/HTML document. It is very common to see people just bind a controller to the HTML `<body>` tag, and let that controller manage the whole application. This works pretty well for small projects, however you can attach one controller to, say, a sidebar or a form, and use another controller for some other part of the document. However, then sharing data from one controller to another becomes a little troublesome, though it is possible handle that (we'll cover that later).

Let's create a small example using a controller:

```html
<html ng-app="stoogeApp">
  <head>
    <script src="http://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.3/angular.min.js"></scriptst>
    <script>
      angular.module('stoogeApp', [])
      .controller('StoogeController', function ($scope){
        $scope.stooges = ['Larry', 'Curly', 'Moe'];
      });
    </script>
  </head>
  <body ng-controller="StoogeController">
    <ul>
      <li ng-repeat="stooge in stooges">{{ stooge }}</li>
    </ul>
  </body>
</html>
```

You see the name of the module is declared, and we set the `ng-app` attribute with a matching name to the html tag, then assign a controller and name it. Then pass it a second argument with a function with any dependencies that are needed inside the function.

Somewhere in Angular, when it's js file is loaded, it set some global variables like `$scope`, which have special abilities. `$scope` specifically is an object we can assign properties to, which is useful for the data binding.

We can add an input to add more stooges:

```html
<html ng-app="stoogeApp">
  <head>
    <script src="http://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.3/angular.min.js"></script>
    <script>
      angular.module('stoogeApp', [])
      .controller('StoogeController', function ($scope){
        $scope.stooges = ['Larry', 'Curly', 'Moe'];
        $scope.addStooge = function() {
          $scope.stooges.push($scope.stoogeName);
          $scope.stoogeName = ''; // clears the input
        };
      });
    </script>
  </head>
  <body ng-controller="StoogeController">
    <form ng-submit="addStooge()">
      <input type="text" ng-model="stoogeName">
      <input type="submit" value="Stooge Me!">
    </form>
    <ul>
      <li ng-repeat="stooge in stooges">
        {{ stooge }}
      </li>
    </ul>
  </body>
</html>
```

No matter where we update a bound `ng-model`, it will get updated on-screen (with some exceptions). This lets us control display-related stuff in the HTML, and keep the javascript code clean, and free of HTML strings, tags, and selectors.
