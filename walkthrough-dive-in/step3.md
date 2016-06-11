> Welcome to my SAPUI5 Walkthrough, And Dive In blog series, where I’ll not only walkthrough the [tutorial](https://sapui5.hana.ondemand.com/sdk/#docs/guide/3da5f4be63264db99f2e5b04c5e853db.html), but dive in, to explore the magic behind SAPUI5. Buckle up and sit tight, it's gonna be a bumpy ride as I’m no expert in any way shape or form, just a humble learner who loves coding and likes to get to the bottom of things =。=

This time, we’ll dive in to find out how a UI5 control (sap.m.Text) is created, let's talk the steps involved from a very high-level:

1. [How does lazy require work?](#how-does-lazy-require-work)
1. [How does a control module get requested, loaded and executed?](#how-does-a-control-module-get-requested-loaded-and-executed)
1. [How does a control object get created?](#how-does-a-control-object-get-created)
1. [How does a control object get turned into HTML?](#how-does-a-control-object-get-turned-into-html)

Break point, checked, alright!

[1]![screenshot#1](/walkthrough-dive-in-screens/step.3.1.png)

How does lazy require work?
---

Before dive into the code, let’s take a quick look at the network tab of the Chrome developer tool, the sap.m.Text module has not yet been loaded, if the module is not there at this moment, how can we create a new instance out of it? (the screenshot number is out of order here, because this section was added after the post published)
[23]![screenshot#23](/walkthrough-dive-in-screens/step.3.23.png)

Yet let's ask a different question, once we click the ’step into’ button in the Chrome developer tool, we get taken to line 27,166, the dummy constructor inside of the lazyRequire method, which loads the class on demand, at what point this dummy constructor get attached to our sap.m.Text class? And what's the deal with lazy require?
[24]![screenshot#24](/walkthrough-dive-in-screens/step.3.24.png)

In order to answer that, let’s take a step back, back to the page initial loading time, that’s when the loadLibrary method called, remember the ‘data-sap-ui-libs=“sap.m”’ we put in the index.html bootstrap? This is when the sap.m library get loaded.
[25]![screenshot#25](/walkthrough-dive-in-screens/step.3.25.png)

Let’s take a look at what’s inside of the sap.m.library.js, we see all the sap.m controls put in a array in a object passed to the initLibrary method. With what we learned in [step 6](step6.md), we know that this method will be called later when the module get executed.
[26]![screenshot#26](/walkthrough-dive-in-screens/step.3.26.png)

When it's called, it then calls the lazyRequire method.

[27]![screenshot#27](/walkthrough-dive-in-screens/step.3.27.png)

Which attaches a dummy constructor for the sap.m.Text class, the dummy constructor method will be called when we try to create a new instance of the class.

[28]![screenshot#28](/walkthrough-dive-in-screens/step.3.28.png)

Alright, with that out of the way, let's continue with `jQuery.sap.require("sap.m.Text")`

How does a control module get requested, loaded and executed?
---

First, we’ll need to require the sap.m.Text module. We talked about how does modules work in detail in [step 6](step6.md), we'll revisit the process here. I recommend you to read the other [post](step6.md) if you ever find the autopsy here is a bit confusing.
[2]![screenshot#2](/walkthrough-dive-in-screens/step.3.2.png)

jQuery.sap.require leads to requireModule.

[3]![screenshot#3](/walkthrough-dive-in-screens/step.3.3.png)

A sync ajax request loads the content of Text-dbg.js, sets it to module.data, and also, sets module.state to LOADED.
[4]![screenshot#4](/walkthrough-dive-in-screens/step.3.4.png)

If we switch to the network tab of Chrome developer tool, we’ll find Text-dbg.js had been downloaded.
[5]![screenshot#5](/walkthrough-dive-in-screens/step.3.5.png)

With module loaded, we’ll then execute the module.
[6]![screenshot#6](/walkthrough-dive-in-screens/step.3.6.png)

We set module state to EXECUTING first, then, have _window.eval to have module data executed.
[7]![screenshot#7](/walkthrough-dive-in-screens/step.3.7.png)

Once done executing the module, its state will be set to READY, and its data will be set to undefined.
[8]![screenshot#8](/walkthrough-dive-in-screens/step.3.8.png)

How does a control object get created?
---

With module ready, we’ll continue creating our Text control, first, to create a shell object oInstance, then, call the constructor method on it.
[9]![screenshot#9](/walkthrough-dive-in-screens/step.3.9.png)

since Control extends from Element, the constructor method of Element will be called next.
[10]![screenshot#10](/walkthrough-dive-in-screens/step.3.10.png)

And Element’s upper chain, the constructor of ManagedObject will also be called.
[11]![screenshot#11](/walkthrough-dive-in-screens/step.3.11.png)

In the ManagedObject constructor method, we’ll register the object in the core.
[12]![screenshot#12](/walkthrough-dive-in-screens/step.3.12.png)

Which leads to registerElement.
[13]![screenshot#13](/walkthrough-dive-in-screens/step.3.13.png)

Which is essentially add our element to the global this.mElements map.
[14]![screenshot#14](/walkthrough-dive-in-screens/step.3.14.png)

After registration, we’ll have settings ({text: “Hello World”} in our case) applied.
[15]![screenshot#15](/walkthrough-dive-in-screens/step.3.15.png)

Which is essentially to call the setText method on “Hello World”.
[16]![screenshot#16](/walkthrough-dive-in-screens/step.3.16.png)

With that, the shell object oInstance is now filled with our Text control properties, and returned. That's all good, but the browser knows only how to render HTML not JavaScript object, what the framework gonna do about it?
[18]![screenshot#18](/walkthrough-dive-in-screens/step.3.18.png)

How does a control object get turned into HTML?
---

Once the document is ready, renderPendingUIUpdates will be one of the tasks to be finished.
[19]![screenshot#19](/walkthrough-dive-in-screens/step.3.19.png)

Who knows how to render our Text control? The framework will get the answer from the control metadata.
[20]![screenshot#20](/walkthrough-dive-in-screens/step.3.20.png)

Then the renderer sap.m.TextRenderer module will be loaded, same old, same old. After that, the render method will be called upon it, with our Text control passed in as its parameter.
[21]![screenshot#21](/walkthrough-dive-in-screens/step.3.21.png)

Lastly, if you take a look at what happen within the render method, you'll see the familiar HTML elements being added to construct the HTML representation of the Text control, that's what will be rendered by the browser.
[22]![screenshot#22](/walkthrough-dive-in-screens/step.3.22.png)

The End.
