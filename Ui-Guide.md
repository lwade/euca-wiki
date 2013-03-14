This is a relatively new document so the information is still a bit sparse. As we continue to fill in the UI infrastructure this document will expand.

# Components

This is a list of the major components of the web UI, what they do and what files they live in:

* index.html - Loads most of the app's logic. In 3.3 we are starting to introduce requirejs based dependency loading of AMD structures.
* explorer.js - contains the top level menu.
* main_container.js - contains the home-grown router.
* templates/eucatable.js - contains the abstract jQuery DataTable based implementation that all the landing pages inherit from
