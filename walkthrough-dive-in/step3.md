> Welcome to my SAPUI5 Walkthrough, And Dive In blog series, where I’ll not only walkthrough the [tutorial](https://sapui5.hana.ondemand.com/sdk/#docs/guide/3da5f4be63264db99f2e5b04c5e853db.html), but dive in, to explore the magic behind SAPUI5. Buckle up and sit tight, it's gonna be a bumpy ride as I’m no expert in any way shape or form, just a humble learner who loves coding and likes to get to the bottom of things =。=

This time, we’ll dive in to find out how a UI5 control (sap.m.Text) is created, let's talk the steps involved from a very high-level:

1. [How does a control module get requested, loaded and executed?](#how-does-a-control-module-get-requested-loaded-and-executed)
2. [How does a control object get created?](#how-does-a-control-object-get-created)
3. [How does a control object get turned into HTML?](#how-does-a-control-object-get-turned-into-html)

Break point, checked, alright, let’s dive in!
[1]![screenshot#1](/screenshots/step.3.1.png)

How does a control module get requested, loaded and executed?
---

First, we’ll need to require the sap.m.Text module. We talked about how does modules work in detail in [step 6](step6.md), we'll revisit the process here. I recommend you to read the other [post](step6.md) if you ever find the autopsy here is a bit confusing.
[2]![screenshot#2](/screenshots/step.3.2.png)

jQuery.sap.require leads to requireModule.
[3]![screenshot#3](/screenshots/step.3.3.png)

A sync ajax request loads the content of Text-dbg.js, sets it to module.data, and also, sets module.state to LOADED.
[4]![screenshot#4](/screenshots/step.3.4.png)

If we switch to the network tab of Chrome developer tool, we’ll find Text-dbg.js had been downloaded.
[5]![screenshot#5](/screenshots/step.3.5.png)

With module loaded, we’ll then execute the module.
[6]![screenshot#6](/screenshots/step.3.6.png)

We set module state to EXECUTING first, then, have _window.eval to have module data executed.
[7]![screenshot#7](/screenshots/step.3.7.png)

Once done executing the module, its state will be set to READY, and its data will be set to undefined.
[8]![screenshot#8](/screenshots/step.3.8.png)

How does a control object get created?
---

With module ready, we’ll continue creating our Text control, first, to create a shell object oInstance, then, call the constructor method on it.
[9]![screenshot#9](/screenshots/step.3.9.png)

since Control extends from Element, the constructor method of Element will be called next.
[10]![screenshot#10](/screenshots/step.3.10.png)

And Element’s upper chain, the constructor of ManagedObject will also be called.
[11]![screenshot#11](/screenshots/step.3.11.png)

In the ManagedObject constructor method, we’ll register the object in the core.
[12]![screenshot#12](/screenshots/step.3.12.png)

Which leads to registerElement.
[13]![screenshot#13](/screenshots/step.3.13.png)

Which is essentially add our element to the global this.mElements map.
[14]![screenshot#14](/screenshots/step.3.14.png)

After registration, we’ll have settings ({text: “Hello World”} in our case) applied.
[15]![screenshot#15](/screenshots/step.3.15.png)

Which is essentially to call the setText method on “Hello World”.
[16]![screenshot#16](/screenshots/step.3.16.png)

With that, the shell object oInstance is now filled with our Text control properties, and returned. That's all good, but the browser knows only how to render HTML not JavaScript object, what the framework gonna do about it?
[18]![screenshot#18](/screenshots/step.3.18.png)

How does a control object get turned into HTML?
---

Once the document is ready, renderPendingUIUpdates will be one of the tasks to be finished.
[19]![screenshot#19](/screenshots/step.3.19.png)

Who knows how to render our Text control? The framework will get the answer from the control metadata.
[20]![screenshot#20](/screenshots/step.3.20.png)

Then the renderer sap.m.TextRenderer module will be loaded, same old, same old. After that, the render method will be called upon it, with our Text control passed in as its parameter.
[21]![screenshot#21](/screenshots/step.3.21.png)

Lastly, if you take a look at what happen within the render method, you'll see the familiar HTML elements being added to construct the HTML representation of the Text control, that's what will be rendered by the browser.
[22]![screenshot#22](/screenshots/step.3.22.png)

The End.
