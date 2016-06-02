In this article, I'll try to build a JavaScript front-end MVC 'framework' in 30 lines of code XD, with this new 'awesome' yet 'powerful' 'framework', you'll be able to create a button that has the 'Hello World' value on it, sounds like something fancy you wanna do? Let's get into it.

![image](https://cloud.githubusercontent.com/assets/5327840/15738521/4a293c20-28de-11e6-81da-b291869ddc5b.png)
 
Let's first take a look at how our button is implemented with this new 'framework'.

```javascript
(function(){
    new Controller(function() {
        var myButton = new View({
            type: 'button'
        });
        var myModel = new Model();
        myModel.bindElement(myButton);
        myModel.setData('Hello World');
    });
}())
```

Here we created a controller, within the controller, we created a button control by instantiating the View class, then we created a new model and set its data to 'Hello World'. That's the MVC that we all familiar with, in the most simplicity terms, View for presentation, Model for holding the data, and Controller that clues the Model and the View together.

With that in mind, we now code up this 'framework'.

First, we'll need three 'class', Model, View, and Controller.

```javascript
function Model() {}

function View() {}

function Controller() {}
```

Let's work on the View first, the View class has one instance method, as its name suggested, it creates a html element, then put it on the page.

```javascript
function View(metadata) {
    return this.render(metadata.type);
}

View.prototype.render = function(type) {
    var element = document.createElement(type);
    document.body.appendChild(element);
    return element;
};
```

Next, we'll move on to the Model.

```javascript
function Model(data) {
    this._data = data;
}

Model.prototype.setData = function(data) {
    this._data = data;
};
```

The first iteration is very straight forward. Next, we'll take care of the interaction between the Model and the View, here's what we'll want to accomplish:
- User should be able bind the view to the model.
- The view should be updated automatically when there's changes to the model which the view was binded.

In order to do that, that's where we introduce the observer pattern into our framework.

```javascript
function Model(data) {
    this._data = data;
    this._observers = [];
}

Model.prototype.setData = function(data) {
    this._data = data;
    this._observers.forEach(function(observer) {
        observer.call(this, data);
    }, this);
};

Model.prototype._addObserver = function(observer) {
    this._observers.push(observer);
};

Model.prototype.bindElement = function(element) {
    this._addObserver(function(data) {
        element.innerHTML = data;
    });
};
```

Dang, dang, dang! The Model class has two public instance methods, `bindElement` and `setData`. (as a side note, as the best practice, we probably should use the JavaScript modular pattern, to hide the private attribute and method, expose only the public ones, but we took the easy way out for simplicity sake). What the `bindElement` method essential does, is to add the function that changes the view to the observer queue, notice the view (element) is kept inside of the inner function with JavaScript's closure mechanism, which enables the view (element) be available later after the `bindElement` method had been executed and popped out of the stack. And the `setData` method loops through the queue, executes the observer (function), as the result of it, our view get updated, mission accomplished XD

![image](https://cloud.githubusercontent.com/assets/5327840/15738558/809eb8a2-28de-11e6-8c3c-7054eea0de9d.png)

Lastly, again, we'll take a easy way out with our Controller class.

```javascript
function Controller(callback) {
    callback();
}
```

Here's the final version of our 30 lines 'framework'.

```javascript
function Model(data) {
    this._data = data;
    this._observers = [];
}

Model.prototype.setData = function(data) {
    this._data = data;
    this._observers.forEach(function(observer) {
        observer.call(this, data);
    }, this);
};

Model.prototype._addObserver = function(observer) {
    this._observers.push(observer);
};

Model.prototype.bindElement = function(element) {
    this._addObserver(function(data) {
        element.innerHTML = data;
    });
};

function Controller(callback) {
    callback();
}

function View(meta) {
    return this.render(meta.type);
}

View.prototype.render = function(type) {
    var element = document.createElement(type);
    document.body.appendChild(element);
    return element;
};
```

> p.s. Stay hungry stay foolish, and happy coding :+1: 
