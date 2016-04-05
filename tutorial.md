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

## 2.0 - Defining our Notices service

The largest change we will face as Drupal 8 developers coming from older versions is the wide spread adoption of *object oriented PHP* throughout our environment. As discussed in our presentation, PHP has been evolving as a language towards more OO constructs. 

One of the core pillars of OO programming is isolation. Towards this goal Drupal allow us to isolate much of our modules logic in what we call a **Service**. A service is the similest type of object we will explore in Drupal 8. It is very easy to over complicate the idea once we start talking about Dependency Injection... but at the end of the day *it is a vanilla PHP object*. It does not have to extend off of any base class. Our services should be focused on the specific problem we are trying to solve. 

Lets dive in.

### 2.1 Designing the NoticesManager service

Knowing that a service is simply a PHP object that exposes the core functionality of the problem we are trying to solve. Lets take a step back and think about what we are trying to solve: 

```
The goal of our module will be to build a tool that will allow us to configure notices which will be displayed only for certain roles
```

Leaving any knowedge of Drupal development behind, fundamentally we will need code to do the following for us: 

1. Get notices for a given user in our system. 
2. Set notices for any role in our system. 
3. Get notices for any role in our system. 

We will encapsulate this functionality into a single service. Lets call this service the NoticesManager.  Now let go ahead and create a file in the location below: 

```
/modules/custom/role_notices/src/NoticesManager.php
```

Ok great lets go ahead and build this bad boy out. Open this file in your editor and lets get building!  

In the first piece of our service we will define our class name, define our namespace, and declare some of the other objects we will be using: 

```
<?php
/**
 * @file
 * Contains \Drupal\role_notices\NoticesManager.
 */

namespace Drupal\role_notices;

use Drupal;
use Drupal\Core\Session\AccountInterface;
use Drupal\Core\State\StateInterface;
use Drupal\Core\Cache\Cache;
```
You will notice we will be leveraging the AccountInterface and the StateInterface. The AccountInterface is quite simply our path to accessing the current user. The StateInterface is how we are going to store our notices. This is new to Drupal 8 BUT familar to those with Drupal experience. This is basically a replacement of our variables system in earlier versions. 

Lets go ahead and declare some protected member variables and declare our class name: 

```
class NoticesManager {

  /**
   * The state service.
   *
   * @var \Drupal\Core\State\StateInterface
   */
  protected $state;

  /**
   * The current user.
   *
   * @var \Drupal\Core\Session\AccountInterface
   */
  protected $user;

  //all of our code will live here. 
}
```
OK now for our contructor. Now this starts to get interesting: 

```
  /**
   * Creates a new Notices Manager.
   *
   * @param \Drupal\Core\Session\AccountInterface $user
   *   The current user.
   * @param Drupal\Core\State\StateInterface $state
   *   Used to get and save notices from site state.
   */
  public function __construct(AccountInterface $user, StateInterface $state) {
    $this->user = $user;
    $this->state = $state;
  }
```
Our contructor takes two parameters. The current user and the StateInterface. What we are seeing here is called Dependency Injection, more specifically contructor-based dependency injection. This is a popular, and powerful way to decouple the objects in our system. By injecting an objects dependencies we are not making hard, tightly coupled dependenices to other areas of the system. This makes our code better isolated and easier to test. 

Ok lets move on to the methods that implement our functionality. Our first method will return all users notices for a particular users set of roles: 

```
  /**
   * Utility function to get the notices of the current user.
   *
   * @return array
   *   An array of notices for the current user. keyed by role id(rid)
   */
  public function getUserNotices() {
    $all_notices = $this->state->get('role_notices_notices');
    $user_notices = array_intersect_key($all_notices, array_flip($this->user->getRoles()));

    return $user_notices;
  }
  ```
  
Here we see how we can use the StateInterface to retrieve a given key from the State API. From there we filter out only the messages for the roles of the user with some nifty array functions. Finally we return the notices. 

Now lets implement a simplier method that will return all notices in the system: 

```
/**
   * Get notices for all roles.
   *
   * @return array
   *   Any associative array of notices.
   *    - keys are machine names of roles
   *    - values are notices strings
   */
  public function getAllNotices() {
    return $this->state->get('role_notices_notices');
  }
```

We are cooking! 

Lets now develop a method that will take an array of notices as a parameter and update the notices in our system: 

```
  /**
   * Set notices for all roles.
   *
   * The notices are saved using Drupal::state()->set().
   * We don't want this setting to be exportable.
   * If we wanted it to be exportable we would use Drupal::config
   *
   * @param array $notices
   *   Any associative array of notices.
   *    - keys are machine names of roles.
   *    - values are notices strings.
   */
  public function setAllNotices(array $notices) {
    // Save the updated keys before saving the new notices.
    $updated_keys = $this->getUpdatedNotices($notices);
    $this->state->set('role_notices_notices', $notices);
    /*
     * Delete render tags for updated Notices.
     * This ensures that if a user has a role that with updated notice they will
     * see the new value.
     * It also ensures if they have a role that was deleted they won't see the
     * old notice.
     */
    if ($updated_keys) {
      Cache::invalidateTags($this->getRenderTags($updated_keys));
    }
  }
```
So we don't overwrite all the notices in our system when we run this method we will call another method, getUpdateNotices which will merge the new notices with already exsisting notices:

```
/**
   * Return the rids for any Notices that have been updated.
   *
   * This all includes new roles and roles that were deleted.
   *
   * @param array $new_notices
   *   Notices that were just submitted to be saved.
   *   The haven't been saved yet.
   *
   * @return array
   *   Role Ids that have been updated or no longer exist.
   */
  protected function getUpdatedNotices(array $new_notices) {
    $current_notices = $this->getAllNotices();
    $diff = array_diff_assoc($current_notices, $new_notices)
      + array_diff_assoc($new_notices, $current_notices);
    return array_keys($diff);
  }
```

We are almost there. The last method we will need pertains to caching. Drupal 8 has a very powerful cache system that allows us to selectivly cache certain parts of the page. We are *tag* certain information as cachable using render tags. This method builds a array of tags we will use to invalidate the cache when we update messages: 

```
  /**
   * Get the render tags for given rids.
   *
   * @param array $rids
   *   Role Ids.
   *
   * @return array
   *   Render tags in the format: 'role_notices:[ROLE_ID]'.
   */
  public function getRenderTags(array $rids) {
    $tags = array();
    if ($rids) {
      foreach ($rids as $rid) {
        $tags[] = 'role_notices:' . $rid;
      }
    }
    return $tags;
  }
  ```
Awesome... we just build our first service in Drupal 8, great work! 
  
But wait, now what? How do we use this? 

In the next section we will need to tell Drupal that our service exsists and inject any dependcies we require. Lets keep moving... 





