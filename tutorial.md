# Drupal Developer Training Exercise!

### Goals
The goal of this exercise is to work out our understanding of Drupal 8 module development. This document contains a series of serveral sections. Each sections will bring us closer to our fully functioning module.

The goal of our module will be to build a tool that will allow us to configure notices which will be displayed only for certain roles. The module will take us through many of the core concepts of Drupal 8 module development. All code for this module will be available here (@todo add link) 

### Table of Contents
- [Preface](#preface)
  - [Prerequisites](#prereqs)
  - [Setting up your development environment](#environment)
- [1.0 - Structure of our module](#preface)
  - [1.1 Where will our module live?](#modlive)
  - [1.2 Creating our .info.yml file](#infoyml)
- [2.0 - Defining our Notices service](#service)
  - [2.1 Designing the NoticesManager service](#noticeManager)
  - [2.2 Registering our service](#register)
- [3.0 Defining our first route](#route)
  - [3.1 Our configuration form](#form)
  - [3.2 Lets tell Drupal about it](#route)
- [4.0 Displaying our our notices](#display)
  - [4.1 Defining a display field](#displayField)
  - [4.2 Populating  our display field with notices](#displaying)
- [5.0 Bonus: Defining a block](#blocks)
  - [5.1 Where plugins live](#plugins)
  - [5.2 Extending BlockBase](#defineBlock)
- [6.0 Wrapping up](#wrap)
  - [6.1 Review core concepts](#coreConcepts)
  - [6.2 Further exploration](#movingForward)

##Preface

### Prerequisites
A basic understanding of PHP programming and some experience with the Drupal content management system is helpful but not required. We will work together to guide you through the various topics we explore in this excercise.

### Setting up your development environment
We have optimized cloud based development environments ready to share using Stack Starter. To get setup go to **[stackstarter.io/spin](http://www.stackstarter.io/spin)** and enter spin code **#trainingday** into the spin code field. This will give you access to the newest version Drupal 8, a web based editor, and console access preloaded with Drush and Drupal Console. 

## 1.0 Structure of our module
Like previous versions of Drupal, Drupal 8 is modular in nature. Modules are basically small collections of configuration and code that either extend and/or enhance your Drupal application.  

### 1.1 Where will our module live?
In Drupal 8 our modules live in the `/modules` directory. This is unlike older versions where we placed modules in the `/sites/all/modules` directory. Each module lives in its own directory. You can think of this as your modules own little world were it will be able to integrate and manipulate the larger world around it. There is where we will be doing most of of work. 

It is best practice to place custom modules in a `custom` directory and contributed modules in a `contrib` directory. Although this is not required it provides for better organization of our solution. We will be developing our module today in the following location: 

```
/modules/custom/role_notices
```

Lets go ahead and create these directories. 

### 1.2 Creating our .info.yml file
In order for Drupal to know about the exsistance of our module we need to tell Drupal about it. The is accomplished through the introduction of our first YAML file -- the .info.yml file. 

Unlike previous versions of Drupal this is the only file needed in order to tell Drupal about our module. The name of this file should match the name of our module, `role_notices.info.yml`: 

Lets create this file in the following location:

```
/modules/custom/role_notices/role_notices.info.yml
```

This file contains a bunch of useful information about our module. We will use it to: 

1. Declare the name of our module.
2. Provide a description. 
3. Declare that we are in fact a module and not a theme. 
4. Declare a package for organization on the `Extend` page. 
5. Declare what core version we are targeting. 
6. And finally define any dependcies our module needs to function. 

Lets see how this looks in YAML format: 

```
# This .info.yml files provides the basic information about our module to Drupal
# More: https://www.drupal.org/node/2000204
name: Role Notices
description: 'Display notices per role.'
type: module
package: Demo
core: 8.x
dependencies:
  - block
```

Lets go ahead and place the above into our `role_notices.info.yml` file. Once you are done lets head over the to the `/admin/modules`. 

What do you see?



