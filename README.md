# Social Analytics Express Code Style Guide 

*How to structure you code for Express 4* 

## Table of Contents 

  1. [Routing](#routing)

## Routing

  - **Chainable Route Handlers**: Recommended approach to avoid duplicate route names

    + Create chainable route handlers for a route path
    + Since path is specified in a single location, it helps modularize your routes and improve code structure 
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

    + A `express.router()` is a complete middleware and routing system; often referred to as a "mini-app" 
    + Both `app.route()` and `express.router()` can create chainable route handlers.

## Objects

  - Use the literal syntax for object creation.

    ```javascript
    // bad
    var item = new Object();

    // good
    var item = {};
    ```

  - Don't use [reserved words](http://es5.github.io/#x7.6.1) as keys. It won't work in IE8. [More info](https://github.com/airbnb/javascript/issues/61)

    ```javascript
    // bad
    var superman = {
      default: { clark: 'kent' },
      private: true
    };

    // good
    var superman = {
      defaults: { clark: 'kent' },
      hidden: true
    };
    ```

  - Use readable synonyms in place of reserved words.

    ```javascript
    // bad
    var superman = {
      class: 'alien'
    };

    // bad
    var superman = {
      klass: 'alien'
    };

    // good
    var superman = {
      type: 'alien'
    };
    ```

**[⬆ back to top](#table-of-contents)**
