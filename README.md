# Express Code Style Guide 

*How to structure your code for Express 4. The purpose of this style guide is to provide guidance on building Express 4 apps. We will show the conventions we use and why we use them. Hopefully by following this style guide, all team members will be able to follow a consistent coding structure* 


## Table of Contents 

  1. [LIFT Principle](#lift)
  1. [Folder Structure](#structure) 
  1. [Routing](#routing)


## LIFT Principle

  **LIFT**: Structure your app so that it has a consistent structure that scales well, is modular, and makes it easier to increase developer efficiency when finding code. The LIFT principle helps you open and work on all of the related files for a feature easily. 
  
  1.  Locating your code is easy 
  2.  Identify code at a glance. Files should use decriptive names and you can use longer names.
  3.  Flat structure. It should not require more than 2-3 clicks to find a file. 
  4.  Try to stay DRY (Don't Repeat Yourself) 
  
[More info on LIFT](https://github.com/johnpapa/angularjs-styleguide#application-structure-lift-principle)

## Folder Structure 

  - **Structure your Express 4 app so it is easy to manage and maintain**

  + How you organize the structure of your app project is always the first step. 
  + Express apps are structured a certain way, which this structure is very difficult to change. If you have to find a specific view, route or css file, they are in different folders. This is a limitation of Express but we can still try to keep our code organized. 
  + You cannot organize your code by "feature". For example, in "Login" you can't put all its css, js, view and route code into a `Login` folder.
  + Keep it flat
  + Only create a folder for common features like "logging, progress bars, etc." 
  + 
  
  - **Keep it Flat** 
  
  - **Overall Folder Structure** 
  
  + Core folders are public, routes and views

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
  - **Public** 
  
  + The `public` folder is where all the "static" files are. This means they are files which are not dynamically generated by the server and sent to the user. These static files require the user to download it from the server the user downloads these static files (css, js and images) from your server. That's why it has to be public because the user must have access to it in order to be able to download it. Files requested by the user.
  + The `images`, `javascripts` and `stylesheets` folders is where your own files go. 
  + The `vendor` folder is for all 3rd party or vendor css or js files. Not your files. Files that you should not change. Keep it flat so no need to put in a folder for each plugin, library, etc. 
  
- **Routes** 

+ All routes reside here. Keep it flat so no need to create a new folder for each part. Just use longer file names.
+ Use consistent naming for all routes named after their feature. We don't use pascal or camel casing for file names. Instead we use dash or periods. 

```javascript
<!-- avoid --> 
// putting it in it's own folder
/routes/loginregister/login.js 
```

```javascript
<!-- recommend --> 
// name it after the feature. 
/routes/login.js 
```

- **Middlewares**

+ In Express 4, order of middleware is important in the code. middleware functions are executed sequentially therefore order of middleware is important.
+ call next() to pass control to next middleware, or else request will be left hanging
+ load the middleware at the router level. 
+ optional mount path, where 

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

http://expressjs.com/4x/api.html#app.use


- **Views** 

+ Where the HTML resides. It includes partial views. 
+ We use express-handlebars and
+ Layouts


  - **Naming Conventions**
  
  + Should simply help with finding files and communicating code
  + For views (html), js, or css, you can use this convention: feature dash subfeature. e.g. `facebook-keymetrics.html` which facebook is the feature, keymetrics is the page.





  [More info](http://www.johnpapa.net/angular-growth-structure/)


## Routing

  - **Chainable Route Handlers**

    + Recommended approach to avoid duplicate route names
    + Create chainable route handlers for a route path
    + This is new to Express 4 
    + Since path is specified in a single location, it helps to modularize your routes and improve code structure 
    + Here is example of chained route handlers using `express.router()`:

    ```javascript
    router.route('/book')
      .get(function(req,res) { 
        res.send('get book'); 
      }) 
      .post(function(req,res) {
        res.send('add book'); 
    })
    ```
  - **Use `express.router()` over `app.route()`** 
  
    + Recommended approach to modularize your routes
    + A `express.router()` is a complete middleware and routing system; often referred to as a "mini-app". It can have its own middlware, param and HTTP verb methods. 
    + This is new to Express 4    
    + You can create many `express.router()` which are route instances and use each instance for a specific path, particlular middleware or logic. 
    + Both `app.route()` and `express.router()` can create chainable route handlers.
    + Here is an example of router with middleware specific to this route:
    
    ```javascript
    var express = require('express');
    var router = express.Router();

    // middleware specific to this router
    router.route(function timeLog(req, res, next) {
      console.log('Time: ', Date.now());
      next();
    })
    
    // define the book page route 
    router.route('/book')
      .get(function(req,res) { 
        res.send('get book'); 
      }) 
      .post(function(req,res) {
        res.send('add book'); 
    })

    module.exports = router;
    ```
    
    the router module loaded in app.js 
    
    ```javascript
    var book = require('./routes/book');
    app.use('/', book); 
    ```
