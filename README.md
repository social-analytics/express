# Express Code Style Guide 

*How to structure your code for Express 4. The purpose of this style guide is to provide guidance on building Express 4 apps. We will show the conventions we use and why we use them. Hopefully by following this style guide, all team members will be able to follow a consistent coding structure and our Express 4 web application will be more maintable, readable.* 


## Table of Contents 

  1. [LIFT Principle](#lift-principle)
  1. [Application Structure](#application-structure) 
  1. [Routing](#routing)
  1. [Express Handlerbars](#express-handlebars)
  1. [Middlewares](#middlewares)
  2. [Server Tools](#server-tools)


## LIFT Principle

  **LIFT**: Structure your app so that it has a consistent structure that scales well, is modular, and makes it easier to increase developer efficiency when finding code. The LIFT principle helps you open and work on all of the related files for a feature easily. 
  
  1.  Locating your code is easy 
  2.  Identify code at a glance. Files should use decriptive names and you can use longer names.
  3.  Flat structure. It should not require more than 2-3 clicks to find a file. 
  4.  Try to stay DRY (Don't Repeat Yourself) 
  
[More Info](https://github.com/johnpapa/angularjs-styleguide#application-structure-lift-principle)

## Application Structure 

**Limitation of Express 4**

  + Express apps are structured a certain way which this structure is very difficult to change. If you have to find a specific view, route or css file, they are in different folders. This is a limitation of Express but we can still try to keep our code organized. 
  + You cannot organize your code by "feature". For example, in "Login" you can't put all its css, js, view and route code into a `Login` folder. 

**Core Folders** 
  
  + Core folders are public, routes and views:

  ```javascript 
  /competitor-hunter
    /public
      /images 
      /javascripts
      /stylesheets
      /vendor
    /routes
      /middlewares
    /views
      /layouts
  ```

**Public**
  
  + The `public` folder is where all the "static" files are. This means they are files which are not dynamically generated by the server. These static files are requested by the user for css, js and images. 
  + The `images`, `javascripts` and `stylesheets` folders is where your own files go.
  + The `vendor` folder is for all 3rd party vendor files (css, js). They are files that you should not change. Keep the vendor folder flat so no need to create many folders for each plugin. As vendor files get added, this will become a problem which we may have to solve using Browserify or RequestJS. 

```javascript
<!-- avoid --> 
// putting it in it's own folder

```

```javascript
<!-- recommend --> 
// name it after the feature. 

``` 

**Routes** 

+ All routes reside here. Keep it flat so don't create a folder for each feature. Use longer file names if you need to.
+ Name routes after their feature. We don't use pascal or camel casing for file names. 

```javascript
<!-- avoid --> 
// putting it in it's own folder
/routes/loginregister/login.js 
```

```javascript
<!-- recommend --> 
// name it after the feature. 
/routes/loginregister.js 
```

**Middlewares**

+ In Express 4, middleware functions are executed sequentially therefore order of middleware is important.
+ Call next() to pass control to next middleware.
+ You can load middleware at the application or router level. 
+ There is an optional mount path

+ `router-level-middlewares`, More Info(http://expressjs.com/api.html#middleware.router)

```javascript
<!-- router.js --> // a middleware sub-stack shows request info for any type of HTTP request to /user/:id 
router.use('/user/:id', function(req, res, next) {
  console.log('Request URL:', req.originalUrl);
  next();
}, function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});
``` 

+ `middlewares` for custom middlewares. 2 types of middlewares

```javascript
<!-- middlewares.js --> 
module.exports = { 
  formHandler: function(req, res, next) { 
    next(); 
  }
}
``` 

```javascript 
<!-- app.js --> 
// add your middlewares 
formHandler = require("middlewares");
app.use(formHandler.formHandler);

// initialize routes 
app.use('/', require('./routes/home')); 

```


Another way would be to use your middleware per route:

routes.js:

middlewares = require("middlewares")
module.exports = function(app){
    app.get("route1", middlewares.formHandler, function(req,res){...})
    app.get("route2", function(req,res){...})
}
I hope I answer your questions.



[More Info](http://expressjs.com/4x/api.html#middleware.api)


- **Views** 

+ Where the HTML resides. It includes partial views. 
+ We use express-handlebars and
+ Layouts


  - **Naming Conventions**
  
  + Should simply help with finding files and communicating code
  + For views (html), js, or css, you can use this convention: feature dash subfeature. e.g. `facebook-keymetrics.html` which facebook is the feature, keymetrics is the page.


https://github.com/johnpapa/angularjs-styleguide#application-structure-lift-principle


  [More info](http://www.johnpapa.net/angular-growth-structure/)


## Routing

  **Use `express.router()` over `app.route()`** 

    + Both `app.route()` and `express.router()` can create chainable route handlers. We suggest using `express.route()` to modularize your routes
    + This is new to Express 4    
    + A `express.router()` is a complete middleware and routing system; often referred to as a "mini-app". It can have its own middleware, parameters and HTTP verb methods. 
    + The code convention we use for organizing route paths is `feature-subfeature` instead of `feature/subfeature`. So, the example below we route to the path `/amazon-book` instead of `amazon/book`. 

    ```javascript
    <!-- routes/amazon.js --> 
    // Here is an example of router with middleware specific to this route:
    var express = require('express');
    var router = express.Router();

    // middleware specific to this router
    router.route('/amazon*', function(req, res, next) {
      console.log('Time: ', Date.now());
      next();
    });
    
    // define the book page route, HTTP GET and HTTP POST
    router.route('/amazon-book')
      .get(function(req,res) { 
        res.send('get book'); 
      }) 
      .post(function(req,res) {
        res.send('add book'); 
      });
    
    // define the shoe page route, HTTP GET and HTTP POST 
    router.route('/amazon-shoe')
      .get(function(req,res) { 
        res.send('get shoe'); 
      }) 
      .post(function(req,res) {
        res.send('add shoe'); 
      });

    module.exports = router;
    ```
    
    ```javascript 
    <!-- app.js --> 
    ...
    // the router modules loaded in app.js. each feature in it's own route file.
    app.use('/', require('./routes/amazon'); 
    app.use('/', require('./route/ebay'); 
    app.use('/', require('./route/taobao'); 
    ...
    
    ```
    
  **Chainable Route Handlers**

    + Create chainable route handlers for a route path
    + This is new to Express 4 
    + When you use chainable route handlers, path is specified in a single location. This helps modularize your routes and improve code structure 

  ```javascript 
  <!-- avoid --> 
  var express = require('express'),
      router = express.Router(); 
      
  // route path for get and post is the same
  router.get('/amazon-book', function(req,res) { 
    res.send('get book'); 
  }); 
    
  router.post('/amazon-book', function(req,res) { 
    res.send('add book'); 
  }); 
  ```

  ```javascript
  <!-- recommended -->
  // A chained route handler using express.router()
  router.route('/amazon-book')
    .get(function(req,res) { 
      res.send('get book'); 
    }) 
    .post(function(req,res) {
      res.send('add book'); 
  })
  ```
    
    
##Express Handlebars

How to use the power of the handlebars for our express web app: 

http://stackoverflow.com/questions/21737057/handlebars-with-express-different-html-head-for-different-pages

This is a great question and, in my mind, a glaring weakness in Express's view model. Fortunately, there is a solution: use Handlebars block helpers. Here's the helper I use for this purpose.


##Middlewares

**TODO: do we need to move these packages guide to another document just for npm packages?**

What middleware packages we use in our web app and why. 

###1. Restler 

https://www.npmjs.org/package/restler

Handles our HTTP request. Our old app used it too. 

##Server Tools

What npm packages we use in our web app and why

###1. Cheerio 

[cheerio](http://browsenpm.org/package/cheerio) is a fast, flexible, and lean implementation of core jQuery designed 
specifically for the server. 

**Why?** When I need to manipulate html files using jQuery from the server-side. Sometimes you just can't do it from the client-side. For example, the helper for handlebar-express needs to modify the `class` attribute in a <li> based on the route selected. 



Middleware Pitfalls - common mistakes made by people when using the middleware
https://blog.safaribooksonline.com/2014/03/10/express-js-middleware-demystified/

- order matters
- forgetting next() 
- all middleware and routes share the same "request" and "response" object. be careful of overriding fields of other middlware. 
- middleware do async operations, be careful where you use the next(), otherwise execution will continue without the async operation having been completed.


How to modularize app.js 
http://stackoverflow.com/questions/5055853/how-do-you-modularize-node-js-w-express
http://ponyfoo.com/articles/modularizing-node-applications-with-express
http://www.quora.com/What-is-the-recommended-method-modularizing-and-packaging-my-web-apps-JavaScript-code

example structure
https://github.com/kishorenc/road
https://github.com/Medium/matador
https://github.com/madhums/node-express-mongoose-demo
http://codereview.stackexchange.com/questions/51213/modularize-this-node-js-express-server-code-for-routes-and-session-handle

Password Reset 
http://sahatyalkabov.com/how-to-implement-password-reset-in-nodejs/
