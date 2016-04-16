# Drupal Developer Resources

Helpful Resources as you learn about Drupal Development for D8!

### Table of Contents
- [Drupal Developer Training Day Slides](http://drupal-8-study-group.github.io/2016-april-drupal8-training-day-deck/#/)
- [Overview](#overview)
- [OOP and Symfony](#oop-and-symfony)
	- [Object-Oriented Programming](#object-oriented-programming)
	- [YAML](#yaml)
	- [Dependency Injection & Symfony](#dependency-injection-and-symfony)
- [Tools](#tools)
	- [Drupal Console](#drupal-console)
	- [Drush Make](#drush-make)
- [Configuration Management (CMI)](#configuration-management)
- [Media Management](#media-guide)
- [Extending Drupal](#extending-drupal)
	- [Routing](#routing)
	- [Blocks](#blocks)
	- [Entity API](#entity-api)
- [Theming](#theming)
	- [General](#general)
	- [Standards](#standards)
	- [Twig](#twig)
- [Example Code / Samples](#example-code)
- [Drupal 7 to Drupal 8](#drupal-7-to-drupal-8)
- [Join the Drupal Association!](#join-the-drupal-association)

## Overview
- [Getting Started with D8](https://www.drupal.org/getting-started-d8-bkg-prereq)
- [Exploring Drupal 8 Podcast series](http://www.talkingdrupal.com/exploring-drupal-8)
- [Why the big architectural changes in Drupal 8](http://buytaert.net/why-the-big-architectural-changes-in-drupal-8)
- [Extending and altering Drupal](https://api.drupal.org/api/drupal/core%21core.api.php/group/extending/8)
- [Case Studies on Drupal.org](https://www.drupal.org/case-studies/featured/all/drupal-8.x)

## OOP and Symfony

### Object-Oriented Programming
- [PHP Namespaces Explained](http://daylerees.com/php-namespaces-explained/)
- [Preparing for Drupal 8: PSR-4 Autoloading](https://drupalize.me/blog/201408/preparing-drupal-8-psr-4-autoloading)
- [Object-Oriented PHP for Beginners](http://code.tutsplus.com/tutorials/object-oriented-php-for-beginners--net-12762)
- [Object-oriented code - documentation on Drupal.org](https://www.drupal.org/node/608152)

### YAML
- [YAML](http://www.yaml.org/)
- [YAML vs Annotations Discussion](https://www.drupal.org/node/1683644)

### Dependency Injection and Symfony

- [Dependency injection in Drupal 8, an introduction.](http://blog.openlucius.com/en/blog/dependency-injection-drupal-8-introduction)
- [Services and dependency injection in Drupal 8](https://www.drupal.org/node/2133171)
- [Do you need a Dependency Injection Container?](http://fabien.potencier.org/do-you-need-a-dependency-injection-container.html)
- [Introduction to Dependency Injection - Drupalize.me free videos](https://drupalize.me/videos/introduction-dependency-injection?p=1780)
- [PIMPLE - A simple PHP
Dependency Injection Container](http://pimple.sensiolabs.org/)
- [Symfony documentation about services and dependency injection](http://symfony.com/doc/current/create_framework/index.html)
- [Symfony documentation](http://symfony.com/doc/current/index.html)

## Tools

A couple of tips on Tools:

You will need a robust IDE (PHPStorm, Eclipse, Netbeans): The importance of an IDE goes hand in hand with D8's shift to OOP. You will be extending classes provided by Drupal core when developing modules. An IDE will be able to tell when you are doing this incorrectly and let you know what you will need to do to correct it. It will also accelerate your development in inserting required methods of the class your extending or the interface your implementing.
    
- [XDebug](https://xdebug.org/)
- [Devel](https://www.drupal.org/project/devel) - also remember to enable the **WebProfiler**!
- [Logging (the change from watchdog() to using Logger)](https://drupalize.me/blog/201510/how-log-messages-drupal-8)

### Drupal Console
- [Drupal Console](https://drupalconsole.com/) -- checkout the **learning mode**!
- [What is Drupal Console for me](http://enzolutions.com/articles/2015/01/25/what-is-drupal-console-for-me/)
- [Drupal Console Git Book](https://hechoendrupal.gitbooks.io/drupal-console/)

### Drush Make
- [Drush make documentation](http://www.drush.org/en/master/make/)
- [Drush repository](https://github.com/drush-ops/drush)

## Configuration Management
- [Managing configuration in Drupal 8](https://www.drupal.org/documentation/administer/config)
- [Configuration API in Drupal 8](https://www.drupal.org/developing/api/8/configuration)
- [Features and CMI](https://www.phase2technology.com/blog/announcing-features-for-drupal-8/)
- [Principles of Configuration Management - Part One](https://www.chapterthree.com/blog/principles-configuration-management-part-one)
- [Principles of Configuration Management - Part Two](https://www.chapterthree.com/blog/principles-configuration-management-part-two)
- [The Drupal 8 configuration schema cheat sheet](http://hojtsy.hu/blog/2014-dec-12/drupal-8-configuration-schema-cheat-sheet)

## Media Guide
- [Drupal 8 Media Guide Gitbook by the Drupal Media Team](https://www.gitbook.com/book/drupal-media/drupal8-guide/details)

## Extending Drupal
### Routing
- [Routing system in Drupal 8](https://www.drupal.org/developing/api/8/routing)

### Blocks
- [The Block System is Finally Useful in Drupal 8](https://drupalize.me/blog/201403/block-system-finally-useful-drupal-8)
- [Panels, Blocks, Layouts, and Drupal 8](https://drupalize.me/blog/201502/panels-blocks-layouts-and-drupal-8)
- [Unraveling the Drupal 8 Plugin System](https://drupalize.me/blog/201409/unravelling-drupal-8-plugin-system)
- [Plugin API in Drupal 8](https://www.drupal.org/developing/api/8/plugins)

### Entity API
- [What's New in Drupal 8: Entity Field API](https://drupalize.me/videos/whats-new-drupal-8-entity-field-api?p=2075)
- [Entity API - Drupal 8](https://www.drupal.org/developing/api/entity)

## Theming

## General
- [Ultimate Guide to Drupal 8: Episode 5 - Front-End Developer Improvements](https://dev.acquia.com/blog/ultimate-guide-drupal-8-episode-5-front-end-developer-improvements)
- [Theming Drupal 8](https://www.drupal.org/theme-guide/8)
- [A Tale of Two Base Themes in Drupal 8 core](https://www.lullabot.com/articles/a-tale-of-two-base-themes-in-drupal-8-core)
- [Theming in Drupal 8 - David Hernandez deck from NJ Drupal Camp 2016](https://www.drupalcampnj.org/sites/default/files/sessions/Theming%20in%20Drupal%208.pdf)
- [D8 Theming Guide](http://sqndr.github.io/d8-theming-guide/index.html)
- [Drupal Template Helper - Chrome Extension for D7 & D8](https://github.com/arshad/drupal-template-helper)

## Standards
- [SMACSS](https://smacss.com/)
- [Introduction to BEM](http://getbem.com/introduction/)
- [BEM](https://en.bem.info/)

## Twig
- [Twig documentation](http://twig.sensiolabs.org/documentation)
- [Let's Debug Twig in Drupal 8!](https://drupalize.me/blog/201405/lets-debug-twig-drupal-8)

## Example Code
- [Examples project](https://www.drupal.org/project/examples)
- [Pistashio example/training theme](https://www.drupal.org/project/pistachio)
- [Hello World Custom module](https://www.drupal.org/node/2464195)
- [Role Notices training module](https://www.drupal.org/project/role_notices)

## Drupal 7 to Drupal 8
- [Converting 7.x modules to 8.x](https://www.drupal.org/update/modules/7/8)
- [Converting from PHP template to Twig](https://www.drupal.org/node/2025313)
- [Configuration management: Drupal 7 to Drupal 8](http://nuvole.org/blog/2014/jun/06/configuration-management-drupal-7-drupal-8)
- [Status of top 100 Drupal 7 modules for Drupal 8](http://www.bluespark.com/status-top-100-contributed-modules-drupal-8)
- [Drupal 8 module status](http://d8status.md-systems.ch/) (auto-updated daily)
- [Change records for Drupal core](https://www.drupal.org/list-changes/drupal)

## Join the Drupal Association!
- [Drupal Association](https://assoc.drupal.org/home)







