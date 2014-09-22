# Express Code Style Guide 

*How to structure your code for Express 4* 

## Table of Contents 

  1. [Routing](#routing)

## Routing

  - **Chainable Route Handlers**: Recommended approach to avoid duplicate route names

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
  - **Use `express.router()` over `app.route()`**: Recommended approach to modularize your routes

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
