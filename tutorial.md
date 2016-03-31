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




