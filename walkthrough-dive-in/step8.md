> Welcome to my SAPUI5 Walkthrough, And Dive In blog series, where I’ll not only walkthrough the [tutorial](https://sapui5.hana.ondemand.com/sdk/#docs/guide/3da5f4be63264db99f2e5b04c5e853db.html), but dive in, to explore the magic behind SAPUI5. Buckle up and sit tight, it's gonna be a bumpy ride as I’m no expert in any way shape or form, just a humble learner who loves coding and likes to get to the bottom of things =。=

This is a fairly straight forward step, but let’s go a little bit further than what the tutorial shows us.

Let’s first create an i18n_de.properties file.

[1]![screenshot#1](/screenshots/step.8.1.png)

And also, make German as the default language in the Chrome browser language settings.
[2]![screenshot#2](/screenshots/step.8.2.png)

With those preparations done, let’s run our application, and see what will happen and more importantly, ask the question how.

We see german on the screen, yeah.

[3]![screenshot#3](/screenshots/step.8.3.png)

Here comes the questions:

(1) How does SAPUI5 pick up the browser language setting.
(2) How does SAPUI5 know which i18n file to load.
(3) How does {0} turn into ‘World’.

(1) How does SAPUI5 pick up the browser language setting.

During the Core class instantiation process, a new Configuration object will be created.

[4]![screenshot#4](/screenshots/step.8.4.png)

The configuration object will pick up our Browser language settings, and make the first entry (de) as the default language value.
[5]![screenshot#5](/screenshots/step.8.5.png)

(2) How does SAPUI5 know which i18n file to load.

We go back to the App.controller.js to instantiate an i18nModel object.

[6]![screenshot#6](/screenshots/step.8.6.png)

During the instantiation process, we’ll get the language code from the configuration object.
[7]![screenshot#7](/screenshots/step.8.7.png)

It continues to construct the correct url (base url + the sLocale code) for requesting the resource.
[8]![screenshot#8](/screenshots/step.8.8.png)

Having the resource loaded.

[9]![screenshot#9](/screenshots/step.8.9.png)

(3) How does {0} turn into ‘World’.

Start with getText method call.

[10]![screenshot#10](/screenshots/step.8.10.png)

getText invokes jQuery.sap.formatMessage method.

[11]![screenshot#11](/screenshots/step.8.11.png)

Which does its regular expression magic to replace the pattern '{0}' with value ‘World’.
[12]![screenshot#12](/screenshots/step.8.12.png)

[13]![screenshot#13](/screenshots/step.8.13.png)

Mission completed  

[14]![screenshot#14](/screenshots/step.8.14.png)
