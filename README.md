# Express Code Style Guide 

*How to structure your code for Express 4. The purpose of this style guide is to provide guidance on building Express 4 applications by showing conventions that we use and why we choose them. Hopefully by following this style guide, our frontend team can have a consistent code structure.* 


## Table of Contents 

  1. [Folder Structure](#structure) 
  1. [Routing](#routing)

## Folder Structure 

  - **Structure your Express 4 app so it is easy to manage and maintain**

  + Express apps are structured a certain way, which this structure is very difficult to deviate from. If you have to find a specific view, route or css file, they are in different folders. This is a limitation of Express but we can still try to keep our code organized. 
  + You cannot organize your code by "feature". For example, in "Login" you can't put all its css, js, view and route code into a `Login` folder.
  + Keep it flat
  + Only create a folder for common features like "logging, progress bars, etc." 
  

  - **File naming**
  
  + 





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
