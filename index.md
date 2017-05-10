# Table of contents

* [About Manoa Munchies](#about-manoa-munchies)
* [User Guide](#user-guide)
* [Developer Guide](#developer-guide)
* [Application design](#application-design)
  * [Directory structure](#directory-structure)
  * [Import conventions](#import-conventions)
  * [Naming conventions](#naming-conventions)
  * [Data model](#data-model)
  * [CSS](#css)
  * [Routing](#routing)
  * [Authentication](#authentication)
  * [Authorization](#authorization)
  * [Configuration](#configuration)
  * [Quality Assurance](#quality-assurance)
    * [ESLint](#eslint)
* [Development history](#development-history)
  * [Milestone 1: Mockup development](#milestone-1-mockup-development-wip)
  * [Milestone 2: Collections and Functionality](#milestone-2-collections-and-functionality)
  * [Milestone 3: Collection Revision, Functionality, and Testing ](#milestone-3-collection-revision,-functionality,-and-testing)

# About Manoa Munchies
Manoa Munchies is about providing students of UH Manoa a better dining experience at the University of Hawaii at Manoa through providing more digestible information which allows the user to decide what food they want on any given day. In addition to providing a better dining experience for the consumers, food vendors will be able to provide information to potential customers of what food is available on the menu for all days of the week and their hours of operation.

# User Guide

## Landing Page
User first arrives on the landing page where they are greeted/informed of what the app does and the services it offers.
![](doc/landing-page.PNG)

## UH Login
Clicking Login from the Landing page pulls up UH Login popup. Users with UH accounts are able to sign in which routes them to profile.
![](doc/cas-login.PNG)

## Profile
The profile page gives information about the user and updates their favorites/preferences on their user homepage based upon their preferred tags. The first time login will initialize the profile to the database. Information given in the profile form page will be shown when other users try to find friends. Profile picture will be updated reactively.
![](doc/edit-profile-page.PNG)
![](doc/edit-profile-page-2.PNG)

## User Homepage
User homepage allows the user to search for a certain craving that they have. This can be done on any page via the top navigation search bar. The User homepage provides the user of the top picks of the day as well as their favorite food items that are available for that day.
![](doc/user-home-page-1.PNG)
![](doc/user-home-page-2.PNG)

## Find Friends
Users will be able to find their friends and be able to use the friend as a tag to find similar preferred/favorite foods. Currently can only sort by tastes. Users are not added until they have given they're "tastes."
![](doc/find-friends-page.PNG)

## Search results
Search results will filter through results of food items based on the string inputted by the user, current availability, time frame availability, friend tags, price, and tastes. Currently can only sort by tastes. Mousing over a card gives the option to favorite the munchie or food item.
![](doc/your-feed-page.PNG)

## Search for vendors and Look at their page
Search for vendors by taste/acceptance of certain payments and visit their page to see the whole menu. The menu can also be sorted if individuals wish to only see certain food items. However, sorting has not yet been implemented due to vendor collections not working at this present time.
![](doc/vendor-search-page-updated.PNG)
![](doc/vendor-profile-page.PNG)

# Developer Guide

Ensure that the latest version meteor and most stable version of node is run on your system, if not [install Meteor](https://www.meteor.com/install).

Afterwards, clone or [download a copy](https://github.com/tasteofmanoa/manoa-munchies/archive/master.zip)
of the repository to your system.

After successfully cloning/downloading the repository, open command prompt or terminal and enter the following commands in the app directory of the repository.

```
$ meteor npm install
```
```
$ meteor npm run start
```

If your install of our application is successful, you should see the app in your browser at [http://localhost:3000](http://localhost:3000).

If any changes are made to any of the sub-directories of app/imports/api, it is required to execute:
```
$ meteor reset
```
As standard with changing any collection.

# Application Design

## Directory structure

The top-level directory structure contains:

```
app/        # holds the Meteor application sources
config/     # holds configuration files, such as settings.development.json
.gitignore  # don't commit IntelliJ project files, node_modules, and settings.production.json
```
The separation of the actual meteor app and the config files, .json, and .ignore allow for better project management and avoid excess clutter. Consolidation of all files in a single directory is not advised for directory readability.

The app/ directory has this top-level structure:

```
client/
  lib/           # holds Semantic UI files.
  head.html      # the <head>
  main.js        # import all the client-side html and js files.

imports/
  api/           # Define collection processing code (client + server side)
    base/
    munchie/     # Collection for food items, used for favorites and vendor menus.
    profile/     # Profiles for users
    taste/       # associated tags with food items/vendors
    vendor/      # Vendors of UH,
  startup/       # Define code to run when system starts up (client-only, server-only)
    client/        
    server/        
  ui/
    components/  # templates of common components/forms that used for web development.
    layouts/     # Layouts contain the templates that act as a base for pages that share
                   common component templates.
    pages/       # Pages are navigated to by FlowRouter routes.
    stylesheets/ # CSS customizations, if any.

node_modules/    # managed by Meteor

private/
  database/      # Holds the required JSON file to initialize the database. If not present,
                   app holds no functionality on build other than profile creation.

public/          
  images/        # holds all the assets for profiles, munchies, vendors, and site graphics.

server/
   main.js       # import all the server-side js files.
```


## Import conventions

This system adheres to the Meteor 1.4 guideline of putting all application code in the imports/ directory, and using client/main.js and server/main.js to import the code appropriate for the client and server in an appropriate order.

This system accomplishes client and server-side importing in a different manner than most Meteor sample applications. In this system, every imports/ subdirectory containing any Javascript or HTML files has a top-level index.js file that is responsible for importing all files in its associated directory.   

Then, client/main.js and server/main.js are responsible for importing all the directories containing code they need. For example, here is the contents of client/main.js:

```
import '/imports/startup/client';
import '/imports/ui/components/form-controls';
import '/imports/ui/components/directory';
import '/imports/ui/components/user';
import '/imports/ui/components/landing';
import '/imports/ui/layouts/directory';
import '/imports/ui/layouts/landing';
import '/imports/ui/layouts/shared';
import '/imports/ui/layouts/user';
import '/imports/ui/pages/directory';
import '/imports/ui/pages/filter';
import '/imports/ui/pages/vendor';
import '/imports/ui/pages/landing';
import '/imports/ui/pages/user';
import '/imports/ui/stylesheets/style.css';
import '/imports/api/base';
import '/imports/api/profile';
import '/imports/api/taste';
import '/imports/api/munchie';
import '/imports/api/vendor';
import '/imports/api/review';

```

Apart from the last line that imports style.css directly, the other lines all invoke the index.js file in the specified directory.

We use this approach to make it more simple to understand what code is loaded and in what order, and to simplify debugging when some code or templates do not appear to be loaded.  In our approach, there are only two places to look for top-level imports: the main.js files in client/ and server/, and the index.js files in import subdirectories.

Note that this two-level import structure ensures that all code and templates are loaded, but does not ensure that the symbols needed in a given file are accessible.  So, for example, a symbol bound to a collection still needs to be imported into any file that references it.

## Naming conventions

This system adopts the following naming conventions:

  * Files and directories are named in all lowercase, with words separated by hyphens. Example: accounts-config.js
  * "Global" Javascript variables (such as collections) are capitalized. Example: Profiles.
  * Other Javascript variables are camel-case. Example: collectionList.
  * Templates representing pages are capitalized, with words separated by underscores. Example: Directory_Page. The files for this template are lower case, with hyphens rather than underscore. Example: directory-page.html, directory-page.js.
  * Routes to pages are named the same as their corresponding page. Example: Directory_Page.


## Data model

The Manoa Munchies data model is implemented by two Javascript classes: MunchieCollection, ProfileCollection, ReviewCollection, TasteCollection, and VendorCollection. All of these classes encapsulate a MongoDB collection with the same name and export a single variable that provides access to that collection. 


## CSS

The application uses the [Semantic UI](http://semantic-ui.com/) CSS framework. To learn more about the Semantic UI theme integration with Meteor, see [Semantic-UI-Meteor](https://github.com/Semantic-Org/Semantic-UI-Meteor).

The Semantic UI theme files are located in app/client/lib/semantic-ui directory. Because they are located in the client/ directory and not the imports/ directory, they do not need to be explicitly imported to be loaded. (Meteor automatically loads all files into the client that are located in the client/ directory).

Note that the user pages contain a menu fixed to the top of the page, and thus the body element needs to have padding attached to it.  However, the landing page does not have a menu, and thus no padding should be attached to the body element on that page. To accomplish this, the router uses "triggers" to add an remove the appropriate classes from the body element when a page is visited and then left by the user.

## Routing

For display and navigation among its four pages, the application uses [Flow Router](https://github.com/kadirahq/flow-router).

Routing is defined in imports/startup/client/router.js.

## Authentication

For authentication, the application uses the University of Hawaii CAS test server, and follows the approach shown in [meteor-example-uh-cas](http://ics-software-engineering.github.io/meteor-example-uh-cas/).

When the application is run, the CAS configuration information must be present in a configuration file such as config/settings.development.json.

Anyone with a UH account can login and use Manoa Munchies to create a portfolio.  A profile document is created for them if none already exists for that username.

## Authorization

The landing is public; anyone can access this page.

The profile and filter pages require authorization: you must be logged in (i.e. authenticated) through the UH test CAS server, and the authenticated username returned by CAS must match the username specified in the URL.  So, for example, only the authenticated user `johnson` can access the pages `http://localhost:3000/johnson/profile` and  `http://localhost:3000/johnson/filter`.

To prevent people from accessing pages they are not authorized to visit, template-based authorization is used following the recommendations in [Implementing Auth Logic and Permissions]https://kadira.io/academy/meteor-routing-guide/content/implementing-auth-logic-and-permissions).

The application implements template-based authorization using an If_Authorized template, defined in If_Authorized.html and If_Authorized.js.

## Configuration

The config directory is intended to hold settings files.  The repository contains one file:
config/settings.development.json

The .gitignore file prevents a file named settings.production.json from being committed to the repository. So, if you are deploying the application, you can put settings in a file named settings.production.json and it will not be committed.

Manoa Munchies checks on startup to see if it has an empty database in initialize-database.js, and if so, loads the file specified in the configuration file, such as settings.development.json.  For development purposes, a sample initialization for this database is in initial-collection-data.json.

## Quality Assurance

### ESLint

Manoa Munchies includes a .eslintrc file to define the coding style adhered to in this application. You can invoke ESLint from the command line as follows:

```
meteor npm run lint
```

ESLint should run without generating any errors.  

It's significantly easier to do development with ESLint integrated directly into your IDE (such as IntelliJ).

# Development History

The development process for Manoa Munchies conformed to [Issue Driven Project Management](http://courses.ics.hawaii.edu/ics314s17/modules/project-management/) practices.


Github project and issue pages were used to manage the progression of the projects using by defining "Milestones" and issues associated with each milestone.

General branch management consists of a branch nomenclature of the following: (issue)(issue #)-(first and last initials). This prevents any mix-ups of which branch belongs to who and one can properly evaluate who worked on what branch just by looking at the branch name.

Documentation of the development history of Manoa Munchies are represented in milestones as shown below.

## Milestone 1: Mockup development WIP
The main objective of the mockup is to get a HTML mockup of what the app should look like without the functionality.

This milestone started on April 5, 2017 and completed on April 13, 2017.

<img width="200px" src="doc/landing-page.PNG"/>
<img width="200px" src="doc/cas-login.PNG"/>
<img width="200px" src="doc/edit-profile-page.PNG"/>
<img width="200px" src="doc/user-home-page-1.PNG"/>
<img width="200px" src="doc/find-friends-page.PNG"/>
<img width="200px" src="doc/your-feed-page.PNG"/>


Milestone one was managed using both [Manoa Munchies GitHub Issues page](https://github.com/tasteofmanoa/manoa-munchies/milestone/1) and [Manoa Munchies GitHub Projects page](https://github.com/tasteofmanoa/manoa-munchies/projects/1). Issues are sorted in order of how integral each item is to the project and/or creates a good foundation for the next issue. For example, the card element from semantic UI is an integral part in the user's experience in each page and, therefore, cards must have a good format in which information is easily digestible.

Milestone 1 was implemented as [Manoa Munchies GitHub Milestone 1](https://github.com/tasteofmanoa/manoa-munchies/milestone/1)
![](doc/m1-issues-page.PNG)

Milestone 1 consisted of seven issues which were all managed in the [Manoa Munchies GitHub Projects page](https://github.com/tasteofmanoa/manoa-munchies/projects/1).
![](doc/m1-project-page.PNG)

Each member is assigned an issue to make a mockup of. However, all members, ideally, will create their own mockup to provide a greater number of prototypes to play with in order to decide what is optimal for the user experience.

As said previously, branch nomenclature goes by (issue)(issue #)-(first and last initials). This prevents any mix-ups of which branch belongs to who and one can properly evaluate who worked on what branch just by looking at the branch name. Each issue when completed will be pushed to master when it is completed.

![](doc/m1-network-graph.PNG)

Milestone 1 was deployed to galaxy under the alias of tasteofmanoa on April 12, 2017.
![](doc/m1-deployment.PNG)

## Milestone 2: Collections and More Functionality
The main objective is to add additional functionality to the application and in order to do that, collections for the database need to be implemented. Major goals for this milestone are to implement the tastes, profile, vendor, and food item collections as well as implement sorting based on the fields within those collections. In addition, producing a script to check for user time once the collections have been implemented will get the app going to near full functionality. In addition, at least five users from the UH community must
New nomenclature branch system: m2-(issue abbreviated)-(initials of dev working on that branch)

This milestone started on April 13, 2017 and completed on April 27, 2017.
The following pages were made/edited.

<img width="200px" src="doc/landing-page.PNG"/>
<img width="200px" src="doc/cas-login.PNG"/>
<img width="200px" src="doc/edit-profile-page.PNG"/>
<img width="200px" src="doc/edit-profile-page-2.PNG"/>
<img width="200px" src="doc/user-home-page-1.PNG"/>
<img width="200px" src="doc/user-home-page-2.PNG"/>
<img width="200px" src="doc/your-profile-page.PNG"/>
<img width="200px" src="doc/find-friends-page.PNG"/>
<img width="200px" src="doc/your-feed-page.PNG"/>
<img width="200px" src="doc/vendor-profile-page.PNG"/>
<img width="200px" src="doc/vendor-search-page.PNG"/>

Profile, Tastes, and Munchies collection implemented and working.

Milestone 2 was implemented as [Manoa Munchies GitHub Milestone 2](https://github.com/tasteofmanoa/manoa-munchies/milestone/2)
![](doc/m2-issues-page.PNG)

Milestone 2 consisted of twelve issues which were all going to be managed in the [Manoa Munchies GitHub Projects page](https://github.com/tasteofmanoa/manoa-munchies/projects/2).
![](doc/m2-project-page.PNG)

Failed to implement review and vendor collection properly as well as time sort functionality.

New nomenclature branch system: m2-(issue abbreviated)-(initials of dev working on that branch). Work was done in branches before committing to master, mainly pulled from master to update branches.
![](doc/m2-network-graph.PNG)

Milestone 2 was deployed to [galaxy](https://manoamunchies.meteorapp.com) on April 27, 2017.
![](doc/m2-deployment.PNG)

## Milestone 3: Collection Revision, Functionality, and Testing
The main goal is to revise the availability/location portions of databases so there aren't as many redundancies in the collections. In addition, more functionality must be added as sorting has not been fully implemented. Sorting based upon time is a key feature and must be implemented by the milestone otherwise it defeats the purpose of this application. In addition, five individuals from the UH community had to test the application.

This milestone started on April 27, 2017 and completed on May 9, 2017.

Vendor search page bug fixed where vendors now populate the results.
![](doc/vendor-search-page-updated.PNG)

Altered availability field within Vendor and Munchie collections to allow for date retrieval in the form of a quadruple (days, start, end, location). Allows for accurate location on days where food trucks change locations.

Filenames, routing names, and template names changed to make more sense intuitively for developers.

Milestone 3 was implemented as [Manoa Munchies GitHub Milestone 3](https://github.com/tasteofmanoa/manoa-munchies/milestone/3)
![](doc/m3-issues-page.PNG)

Milestone 3 consisted of five issues which are all managed in the [Manoa Munchies GitHub Projects page](https://github.com/tasteofmanoa/manoa-munchies/projects/3).
![](doc/m3-project-page.PNG)

Nomenclature branch system: m3-(issue abbreviated)-(initials of dev working on that branch). Work was done in branches before committing to master, mainly pulled from master to update branches.
![](doc/m3-network-graph.PNG)

Milestone 3 was deployed to [galaxy](https://manoamunchies.meteorapp.com) on May 9, 2017.
![](doc/m3-deployment.PNG)

### Initial User Study

The initial user study for manoa munchies observed and took feedback from five UH Manoa community members in which all of them belonged to the ICS program. Individuals recruited for the study were selected based upon availability (in order to maintain quality of the feedback), experience with web development (professionally, took a class, or hobbyist), and necessity to use the application on campus. UH Manoa members were added to the CAS login system for UH if they were not already on it. All user feedback was recorded via google forms.

The following questions were asked of all the individuals in this study:
- How did you test out this app?
- Would you use this app over something like Yelp?
- Would you recommend this app to a friend?
- What aspects of this application do you like?
- What aspects of this application could improve?
- What features would you add to improve this application?
- How would you rate this application?
- Other comments?

#### Results/Analysis
##### How did you test out this app?

  Individuals of this app either had access to UH CAS or had to use a developer account. Only one individual had actually used the developer account while the other most likely misreported because he/she works for UH ITS.
  ![](doc/user-study-1.PNG)


##### Would you use this app over something like Yelp?

  A majority of the users who tested the app reported that they may or may not use the app. This can be attributed to the current state of the project being unable to search and/or filter by time.
  ![](doc/user-study-2.PNG)

##### Would you recommend this app to a friend?

  A majority of the users reported they would recommend this app to their friends. Based on the current build that the users had tested, this most likely stemmed from the potential of the app rather than its current state. However, this shows that there is a demand for such an application for UH students.
  ![](doc/user-study-3.PNG)

##### What aspects of this application do you like?

  Users were very pleased with the front-end design and interface of the application as well as how it searches through menu items as opposed to solely searching/filtering through vendors.
  ![](doc/user-study-5.PNG)

##### What aspects of this application could improve?

  General functionality complaint due to inability to use and social functions, search functions, unimplemented pages, etc. Suggestions taken into consideration would be profile and edit profile should be on the same page.
  ![](doc/user-study-6.PNG)

##### What features would you add to improve this application?

  The main thing users wanted to see that wasn't already being planned to be implemented was a tutorial to provide information on how to use the application.
  ![](doc/user-study-7.PNG)

##### How would you rate this application?

  Average score for the application was a 3.2  which was much higher than the projected rating due to the lack of functionality of the build the users were given.
  ![](doc/user-study-4.PNG)

##### Other comments?

  Second comment makes a valid point about the breadth of the application, but the main point of the application did not get across that it is intended for the UH campus only.
  ![](doc/user-study-8.PNG)

#### Summary
Individuals really enjoyed the overall concept of the app and the ideas that were brought to the table when they tested it. All of them wanted the application to work, functionality wise, and said that the strongest part of the application was its front-end design and interface. Front-end part of the app, in terms of designing and implementing it, was done so well due to it being the most understood concept amongst the group. The user study reflected the weakness of the group which is the backend -- functionality of the application. The thing can be nicely packaged but if it doesn't work, it might as well not be an app yet.  
