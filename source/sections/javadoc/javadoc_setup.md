(javadoc-setup)=

# Setting up JavaDoc with Eclipse

The CPSign-API comes with a jar-file with JavaDoc-documentation of the API classes. Here comes a short guide for setting it up in Eclipse IDE.

Add cpsign-withdeps.jar to your build-path, go to the properties window of your project and “Java Build Path”. In the Libraries tab, press the small arrow to the left of “cpsign-withdeps.jar” to show all information (see picture).

```{image} imgs/javadoc_setup_1.png
```

Select the row “Javadoc location: (None)” and press the “Edit…”-button on the right side. Here you will be prompted with a new pop-up window and should check the radiobutton for “Javadoc in archive” as in the picture below. If you’ve placed the javadocumentation-jar inside of your project, check the radiobutton for “Workspace file” otherwise select the “External file”. Now simply press the “Browse…” button and select the cpsign-api-doc.jar and press OK.

```{image} imgs/javadoc_setup_2.png
```

```{image} imgs/javadoc_setup_3.png
```

If everything went OK, there should be a path to the cpsign-api-doc.jar file instead of saying “(None)” as when we started. It should now be possible to get help through Eclipse.

```{image} imgs/javadoc_setup_4.png
```
