> Welcome to my SAPUI5 Walkthrough, And Dive In blog series, where I’ll not only walkthrough the [tutorial](https://sapui5.hana.ondemand.com/sdk/#docs/guide/3da5f4be63264db99f2e5b04c5e853db.html), but dive in, to explore the magic behind SAPUI5. Buckle up and sit tight, it's gonna be a bumpy ride as I’m no expert in any way shape or form, just a humble learner who loves coding and likes to get to the bottom of things =。=

SAPUI5 uses AMD (Asynchronous Module Definition) format for its module definition and consumption, AMD format is to provide a solution for modular JavaScript that developers can use, you can find a lot of materials out there on this topic, let’s see how does it work in SAPUI5.

This is the code introduced in step 6: modules of the walkthrough tutorial. The MessageToast module is the star of today’s show.

[1]![screenshot#1](/screenshots/step.6.1.png)

How does a module get defined?
---

First let’s take a quick look at How does a module defined? I removed all the methods except the show method of our MessageToast module, so that it can be fit into the screenshot below. We define a module, by calling sap.ui.define, the first parameter could be (1) the name of the module, when omitted, the loader determines the name from the request, that’s what we’re doing here. The rest of the parameters are (2) the array of dependencies and (3) the module factory function, and (4) the boolean depending on whether an export to global names is required.

In the factory function, we define a javascript object called MessageToast, `.show` is one of the method / property added, it returns the MessageToast object in the end, that’s it.

`sap/m/MessageToast.js`

[2]![screenshot#2](/screenshots/step.6.2.png)

Alright, with what’s our MessageToast module looks like in mind, let’s starting debugging, to reveal these mysteries:

- [How does a module get consumed?](#how-does-a-module-get-consumed)
- [What are the module states available?](#what-are-the-module-states-available)
- [How does a module get loaded?](#how-does-a-module-get-loaded)
- [How does a module get executed?](#how-does-a-module-get-executed)
- [How does a module get made available to global name space?](#how-does-a-module-get-made-available-to-global-name-space)

How does a module get consumed?
---

In our example code, the MessageToast module is consumed as one of the dependencies of the App.controller.js.

[3]![screenshot#3](/screenshots/step.6.3.png)

The MessageToast module is required in the requiredAll method which is responsible for resolving the dependencies.

[4]![screenshot#4](/screenshots/step.6.4.png)

Within requireAll, the requireModule method is called.

[5]![screenshot#5](/screenshots/step.6.5.png)

requireModule looks for MessageToast in the global mModules map, if it does not exist, the oModule object will be created, with its first property, state, with value INITIAL.

[6]![screenshot#6](/screenshots/step.6.6.png)

What are the module states available?
---

Before we go any further, let’s take a look at what are the module states available, this would give us a good picture of what’s next to come.

```javascript
INITIAL = 0, // Module neither has been required nor preloaded not declared, but someone asked for it.
PRELOADED = -1, // Module has been preloaded, but not required or declared
LOADING = 1, // Module has been declared.
LOADED = 2, // Module has been loaded, but not yet executed.
EXECUTING = 3, // Module is currently being executed
READY = 4, // Module has been loaded and executed without errors.
FAILED = 5, // Module either could not be loaded or execution threw an error
```

How does a module get loaded?
---

Next, we set module state to LOADING, then, a sync ajax request action is performed, MessageToast.js is retrieved and its content is set to the data property of the oModule object, and also, the module state is now set to LOADED.

One of the advantages of AMD format is that modules can be loaded asynchronously, but as you can see, modules in SAPUI5 is now loaded synchronously, according to the documentation: sap.ui.define currently loads modules with synchronous XHR calls. This is basically a tribute to the synchronous history of UI5.

[7]![screenshot#7](/screenshots/step.6.7.png)

With module loaded, the execModule method is called to execute module.

[8]![screenshot#8](/screenshots/step.6.8.png)

How does a module get executed?
---

We first set module state to EXECUTING, then calls window.eval on the module data.

[9]![screenshot#9](/screenshots/step.6.9.png)

By executing our MessageToast module, we’ll go through the sap.ui.define -> requireAll -> requireEachModule -> execEachModule process again as MessageToast depends on another three modules as we saw earlier. Once dependencies resolved, the callback which we saw at screenshot [5] is called with the aModules array, that’s where the dependencies are kept.

[10]![screenshot#10](/screenshots/step.6.10.png)

In the callback function, the MessageToast factory function is called with the depended modules.

[11]![screenshot#11](/screenshots/step.6.11.png)

That results the MessageToast object filled with all its methods returned and assigned to the content property of oModule object.

[12]![screenshot#12](/screenshots/step.6.12.png)

Next, we’ll be exporting our module to the global name space cause we passed in export true when we (right, not us, but the framework) define the module.

[13]![screenshot#13](/screenshots/step.6.13.png)

How does a module get made available to global name space?
---

jQuery.sap.setObject method is called to do the exporting.

[14]![screenshot#14](/screenshots/step.6.14.png)

Which essentially adds the MessageToast object to the sap.m global namespace / object.

[15]![screenshot#15](/screenshots/step.6.15.png)

Lastly, we set module state to READY, and clear out the data property.

[16]![screenshot#16](/screenshots/step.6.16.png)

Then, we come back, the MessageToast and all its methods, `.show` included are made available to us.

[17]![screenshot#17](/screenshots/step.6.17.png)

Our Hello World is framed with the beautiful MessageToast control.

[18]![screenshot#18](/screenshots/step.6.18.png)

The end, happy coding :)
