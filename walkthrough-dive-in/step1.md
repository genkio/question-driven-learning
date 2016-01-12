> Welcome to my SAPUI5 Walkthrough, And Dive In blog series, where I’ll not only walkthrough the [tutorial](https://sapui5.hana.ondemand.com/sdk/#docs/guide/3da5f4be63264db99f2e5b04c5e853db.html), but dive in, to explore the magic behind SAPUI5. Buckle up and sit tight, it's gonna be a bumpy ride as I’m no expert in any way shape or form, just a humble learner who loves coding and likes to get to the bottom of things =。= 

Happy new year everyone !

Instead of creating a Hello World web page, I got something else prepared for you, let’s dive in to talk about how does the BaseObject, the base class for all SAPUI5 objects get created, where it all began. (btw, it feels strange to me every time seeing the word "class" in the JavaScript context...the base class - BaseObject, what? ←_←)

In the process of doing that, I’ll give my best shot to try to explain things like how does “class” look like in the JavaScript / SAPUI5 context, what’s the implementation behind SAPUI5 inheritance. For the JavaScript / SAPUI5 gurus out there, please please let me know if there’s anything explained incorrectly, thank you.

Okay, before we dive into the framework source code, allow me to talk a bit about OO in JavaScript in general to the best of my knowledge, to prepare our journey. Bare with me, let me try to do that in one single screenshot.

OO in JavaScript
---
screenshot#1

Allow me to add one more drawing to hit these concepts home. (Thanks to Mr.Kyle Simpson's You Don't Know JS book series which helped me greatly to understand these)

JavaScript function, object, prototype, constructor, __proto__ relationships illustrated
---
screenshot#2

I think we’re ready to dive into the framework, see how the BaseObject get created, let’s start by setting a break point here:
screenshot#3
Before we step into the craeteClass method, let’s talk about how does SAPUI5 create a class from a high-level.

The code above (between line 34,963 and 34972) is essentially this if we strip it down to the bare minimum:

```javascript
var BaseObject = function() {
    // complain if 'this' is not an instance of a subclass
    if ( !(this instanceof BaseObject) ) {
        throw Error("Cannot instantiate object: \"new\" is missing!");
    }
}
```

This does look familiar, right? Same as the Person “class” example we had at the beginning of our post, if that’s all we need to create a “class” in JavaScript, what else does SAPUI5 createClass offers? Here’s a couple (not all) of them:

What does createClass method offer?
---

- our class can be made visible as a global JavaScript object (sap.ui.base.Object).
- with the classInfo object, you can define the constructor function separately, which is something you can’t do with vanilla JavaScript, `Klass.prototype.constructor  = function() { // constructor implementation }` won’t work.
- with the classInfo object, you can define metadata to your class as well.
- with the classInfo object, besides constructor and metadata, additional functions can be added into the prototype object.
- our class can pick up a convenient “static” method called extend, which makes our class extensible (inheritable).

Alright, enough with the trailers, let’s step into the Metadata.createClass method and get the show started ! (screenshots below reflect the key points during framework code execution, with inline explanations / narrows)

screenshot#4
screenshot#5
screenshot#6
screenshot#7
screenshot#8
screenshot#9
screenshot#10
Create the metadata.

There’s too much screenshots already, I won’t get into the create metadata in detail, but one thing interesting to look at during the metadata creation process, see how the prototype object get enriched with the classInfo object.

How does prototype object get enriched?
---
screenshot#11
screenshot#12

Just like that, a new class, called BaseObject get created, as you can see, next is that, methods (key value pairs, method name as the key, and function as the value) will be added on the BaseObject prototype object.
screenshot#13

The End.