# Drupal Developer Training Exercise!

### Goals
The goal of this exercise is to work out our understanding of Drupal 8 module development. This document contains a series of sections. Each sections will bring us closer to our fully functioning module.

The goal of our module will be to build a tool that will allow us to configure notices which will be displayed only for certain roles. The module will take us through many of the core concepts of Drupal 8 module development. All code for this module will be [available here](https://www.drupal.org/project/role_notices).

<a name="top"></a>

### Table of Contents
- [Preface](#preface)
  - [Prerequisites](#prerequisites)
  - [Setting up your development environment](#setting-up-your-development-environment)
- [1.0 - Structure of our module](#10-structure-of-our-module)
  - [1.1 Where will our module live?](#11-where-will-our-module-live)
  - [1.2 Creating our .info.yml file](#12-creating-our-infoyml-file)
- [2.0 - Defining our Notices service](#20---defining-our-notices-service)
  - [2.1 Designing the NoticesManager service](#21-designing-the-noticesmanager-service)
  - [2.2 Registering our service](#22-registering-our-service)
- [3.0 Defining our first route](#30-defining-our-first-route)
  - [3.1 Our configuration form](#31-our-configuration-form)
  - [3.2 Lets tell Drupal about it](#32-lets-tell-drupal-about-it)
  - [3.3 Linking up our form](#33-linking-up-our-form)
- [4.0 Displaying our notices](#40-displaying-our-notices)
  - [4.1 Defining a display field](#41-defining-a-display-field)
  - [4.2 Populating  our display field with notices](#42-populating-our-display-field-with-notices)
- [5.0 Bonus: Defining a block](#50-bonus-defining-a-block)
  - [5.1 Where plugins live](#51-where-plugins-live)
  - [5.2 Extending BlockBase](#52-extending-blockbase)
- [6.0 Wrapping up](#60-wrapping-up)
  - [6.1 Review core concepts](#61-review-core-concepts)
  - [6.2 Further exploration](#62-further-exploration)

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

**Lets go ahead and create these directories.**

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

```yaml
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

**What do you see?**

[Back to top](#top)

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

We will encapsulate this functionality into a single service. Lets call this service the NoticesManager.  Now let go ahead and **create a file in the location below**: 

```
/modules/custom/role_notices/src/NoticesManager.php
```

Ok great lets go ahead and build this bad boy out. **Open this file in your editor** and lets get building!

In the first piece of our service we will define our class name, define our namespace, and declare some of the other objects we will be using: 

```php
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

```php
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
OK now for our constructor. Now this starts to get interesting:

```php
  /**
   * Creates a new Notices Manager.
   *
   * @param \Drupal\Core\Session\AccountInterface $user
   *   The current user.
   * @param \Drupal\Core\State\StateInterface $state
   *   Used to get and save notices from site state.
   */
  public function __construct(AccountInterface $user, StateInterface $state) {
    $this->user = $user;
    $this->state = $state;
  }
```
Our constructor takes two parameters. The current user and the StateInterface. What we are seeing here is called Dependency Injection, more specifically contructor-based dependency injection. This is a popular, and powerful way to decouple the objects in our system. By injecting an objects dependencies we are not making hard, tightly coupled dependenices to other areas of the system. This makes our code better isolated and easier to test.

Ok lets move on to the methods that implement our functionality. Our first method will return all users notices for a particular users set of roles: 

```php
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

Now lets implement a simpler method that will return all notices in the system:

```php
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

```php
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

```php
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

```php
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

In the next section we will need to tell Drupal that our service exists and inject any dependencies we require. Lets keep moving...

[Back to top](#top) 

## 2.2 Registering our service

In our last section we dove into implementing our first service. Besides some of the State API and caching mumbojumbo we just implemented a standard PHP object that gets and sets an array. Drupal knows nothing about this object currently. 

This introduces another pattern that will be persistent in our Drupal 8 development lives. Many of the the tasks we perform when developing consists of this idea:

```
Build a tool, and tell Drupal about it. 
```
This was coined by [Larry Garfield (aka Crell) in his Drupal 8 Crash course talk](https://www.youtube.com/watch?v=tmT6CATUWbk). This is very powerful and jives well with the idea that Drupal 8 exposes a much more consistant developer experience then earlier versions. 

So we did the first part, we built a tool. Now lets tell Drupal about it. We will do this through the introduction of our second YAML file. 

Lets create a file in the following location: 

```
/modules/custom/role_notices/role_notices.services.yml
```
This file is going to contain all the information we need to let Drupal know we are exposing a service that will be retrievable from other areas of the system through what we call the *Service Container*. Lets go ahead and build this out, the comments in this file have some good explainations we can follow:  

```yaml
# This file registers this modules service(s).
services:
  # We only need 1 service to get and set our notices.
  # Notice that we use our module machine name as the first part of our service name.
  # This ensures we won't have naming conflict with another module
  role_notices.notice_manager:
    # This is the class that our service will be.
    # This tells the Drupal's service container which class to use for our service instance.
    class: Drupal\role_notices\NoticesManager
    # Our services depends on other services.
    # We declare them as arguments or dependencies of our service so that the
    # Service Container will provide them as arguments to our class's constructor.
    arguments: ['@current_user', '@state']
    #?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?
    # You may be wondering how you would figure where these services are defined.
    # All(most?) Drupal services are declared in *.services.yml
    # So for example to find out where "@current_user is define search like this
    # inside Drupal code base:
    #  text: "current_user:"
    #  file name pattern: *.services.yml
    #
    # Could you find these services? What classes are they?
    # Notices that classes match the types of the arguments to our service's
    # constructor.
    #?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?#?
```

Awesomesauce, we now built a tool, our NoticesManager service, and we told Drupal about it. 

Ok, so are we done? Well our core functionality is built but now we need to build tools that will allow us to actually use this service. 

Lets keep it moving!

[Back to top](#top) 

## 3.0 Defining our first route

A route is a path which is defined for Drupal to return some sort of content on. It is a way we map a path (URL) to a piece of code we write. Drupal 8's routing system replaces parts of hook_menu from previous versions. It is heavily based on Symfony's routing system, so by learning this you are in fact also learning some Symfony!

**So what route do we need to define?**

## 3.1 Our configuration form

We want to expose a way for the users of our module to configure notices for any given role in the system. Towards this goal we would want a form that will expose this functionality. We will be learning two core concepts in this part of the exercise:

1. We will learn how to define form in Drupal 8. (build a tool) 
2. We will learn how to hook up this form to a route (tell Drupal about it)

Lets jump into the first part. Our form will be nicely encapsulated in a PHP class defined in our module. Lets go ahead and create a file in the following location: 

```
/modules/custom/role_notices/src/Form/RoleNoticesSettingsForm.php
```
In this file we will define an object that will enable us to implement all the critical pieces of a form. That is: 

1. Build our form using the Form API style arrays. 
2. Validate our form using a form validator function (we are actually not doing this here but should)
3. Submit and process our form data with a submit handler. 

Let go ahead and start writing some code: 

```php
<?php
/**
 * @file
 * Contains \Drupal\role_notices\Form\RoleNoticesSettingsForm.
 */

namespace Drupal\role_notices\Form;

use Drupal\Core\Form\FormBase;
use Drupal\role_notices\NoticesManager;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\Core\Form\FormStateInterface;
```

Just as we did in our service, we first define our namespace and declare any other objects we will be using in our form implementation.

```php
/**
 * Simple Settings form.
 *
 * This settings form does not save it's settings as config(they need to be
 * updated on the site) so it does not extend Drupal\Core\Form\ConfigFormBase;
 */
class RoleNoticesSettingsForm extends FormBase {

  /**
   * The state service.
   *
   * @var \Drupal\role_notices\NoticesManager
   */
  protected $noticesManager;
  
  //all of our methods go here.  
}
```

**What is one big difference you see in our class definition above?**

That's right, we are extending a base class. The base class we are extending is the *FormBase*. *FormBase* implements the *FormInterface*. This interface provides the blueprint for the required methods we need to implement in order to create a working form. 

The other piece you will notice above is that we are defining a protected member variable that will eventually hold our NoticesManager service. 

Ok lets construct this bad boy:

```php
  /**
   * Class constructor.
   *
   * @param \Drupal\role_notices\NoticesManager $notices_manager
   *   The notices manager for getting and setting notices.
   */
  public function __construct(NoticesManager $notices_manager) {
    $this->noticesManager = $notices_manager;
  }
```
Look at that, there is our nifty constructor based dependency injection again!

Unlike the services YAML file which took care of injecting our dependencies we need for our service, the FormBase also implements the *ContainerInjectionInterface* which requires us to define this next method:

```php
/**
   * Create function to provide our necessary service.
   *
   * This works because we are extending FormBase which implements
   * ContainerInjectionInterface.
   *
   * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
   *   The container that lets us get our service.
   *
   * @link https://www.drupal.org/node/2133171 For more on dependency injection
   */
  public static function create(ContainerInterface $container) {
    return new static(
    /*
     *  Load the service required to construct this class.
     *  Notice that this is the name we use to define our service in role_notices.services.yml
     */
      $container->get('role_notices.notice_manager')
    );
  }
  ```
There are some interesting things happening here. This is what we call a **factory method**. It is responsible for creating a new instance of itself. Not only does it create an instance of itself but it also injects any dependencies it will require by extracting them from the *service container*. 

Pretty cool stuff! 

One last auxiliary method we need to define is the *getFormID* method. Because we do still have the hook system in Drupal 8 we need to tell our procedual code what our form ID is. This enables us to still use features such as `hook_form_alter` and the like:

```php
  /**
   * Returns a unique string identifying the form.
   *
   * @return string
   *   The unique string identifying the form.
   */
  public function getFormID() {
    return 'role_notices_setting_form';
  }
```

Ok with our form now able to create itself, inject its dependencies, and tell some of the legacy hooks who we are lets move onto actually defining our form by implementing the buildForm method: 

```php
/**
   * Form constructor.
   *
   * @param array $form
   *   An associative array containing the structure of the form.
   * @param FormStateInterface $form_state
   *   An associative array containing the current state of the form.
   *
   * @return array
   *   The form structure.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    $role_names = user_role_names(TRUE);
    /*
     * Using an empty container with #tree property
     *  will make our values come back as array.
     */
    $form['role_notices_notices'] = array(
      '#tree' => TRUE,
    );
    $notices = $this->noticesManager->getAllNotices();
    // Create 1 text area for each role.
    foreach ($role_names as $role_id => $role_name) {
      $form['role_notices_notices'][$role_id] = array(
        '#type' => 'textarea',
        '#title' => $role_name,
        '#description' => $this->t('Add a notice for the %role role', array('%role' => "<strong>$role_name</strong>")),
        '#default_value' => isset($notices[$role_id]) ? $notices[$role_id] : '',
      );
    }
    $form['actions'] = array('#type' => 'actions');
    $form['actions']['submit'] = array(
      '#type' => 'submit',
      '#value' => $this->t('Save Text settings'),
    );
    return $form;
  }
  ```
For those of us familiar with Drupal 7 form building this should look familiar. We will still be defining these form arrays in this fashion.

Lets read this code. **What is it doing?** 

Ok, now we will be defining one more method in our form. That is our submit handler: 

```php
/**
   * Form submission handler.
   *
   * @param array $form
   *   An associative array containing the structure of the form.
   * @param FormStateInterface $form_state
   *   An associative array containing the current state of the form.
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $form_values = $form_state->getValues();
    $this->noticesManager->setAllNotices($form_values['role_notices_notices']);
    drupal_set_message($this->t('The notices have been saved.'));
  }
```

This method quite simply gets the form values and passes them off to our service for processing. 

Lets talk about this for a moment and explore how we were able to use our service within the context of this form. 

Great! We just built a form... now what? 

Well... lets tell Drupal about it!

[Back to top](#top) 

### 3.2 Lets tell Drupal about it

Just as we experienced with the development of our service we now have a class implemented but Drupal does not know about it. How do we go about telling Drupal about our form? 

This leads us to the introduction of our third YAML file, the routing.yml file. This file declares paths in our system and defines various properties of these paths. This file lives in our modules root directory.

Lets go ahead and create a file in the following location: 

```
/modules/custom/role_notices/role_notices.routing.yml
```
The name of this file should always following the pattern <module-name>.routing.yml

Alright lets go ahead and build this file out: 

```yaml
# machine name for the route.
# Convention for machine name is module_name.unique_name
role_notices.settings_form:
  # system path that will controlled by form controller
  path: '/admin/people/role_notices'
  defaults:
    # Class that creates the form
    _form: 'Drupal\role_notices\Form\RoleNoticesSettingsForm'
  requirements:
      # Permission the user must have to view this page.
      # We created this permission in role_notices_permission
      _permission: 'administer role notices'
```

Lets talk a little bit about what exactly is happening here. We are defining a machine name for our route, then defining our path. After that we give our route some defaults. For a form we tell the routing system about our form giving it our namespaced class name. After that we give the route some requirements which in our case is a permission we will define in our next YAML, the permissions.yml file. 

Lets go ahead and create the following file: 

```
/modules/custom/role_notices/role_notices.permissions.yml
```

In this file we will define one simple permission: 

```yaml
# Since the access to our new custom pages will be granted based on
# special permissions, we need to define what those permissions are here.
# This ensures that they are available to enable on the permissions
# administration pages.
administer role notices:
  title: 'Administer Role Notices'
```

Easy as that! We now have a form, wired to a path in our system, which uses a custom permission, and leverages our NoticesManager service.

### 3.3 Linking up our form

Ok one last small detail. We want our users to be able to access this form without having to explicitly know the path we just defined. In Drupal 7 this is something we did in hook_menu. This has now bee extracted out to a few YAML files. For our purposes we want to add a local task on the user listing (aka collection) view. Lets go ahead and create the following file: 

```
/modules/custom/role_notices/role_notices.links.task.yml
```

This file will define our menu entry for this route: 

```yaml
# Define the 'local' links for the module
role_notices.settings_form:
  title: 'Role Notices'
  # route_name connects our task to the route defined in role_notices.routing.yml
  route_name: role_notices.settings_form
  # Declaring the base_route will create our task as a tab connected to the menu
  # item for the user.admin_account route
  base_route: entity.user.collection
  weight: 15
```

Good work, lets go ahead and enable our module and see if we can see this in action!

But wait there is more! In our next section we will explore how we actually display these notices to our users.

[Back to top](#top)

## 4.0 Displaying our notices

Up until this point we have done a bunch of work without having to create a .module file or even talk about the hook system! Well, in Drupal 8 the hook system is still part of our lives, just not as big of a part. 

The next piece of the puzzle for our module is to actually display user notices on the users page. In more formal Drupal speak we want to display notices on the user entity view page for any given user in any given role. 

To accomplish this we will be introducing two hooks. These hooks will be implemented in our .module file

So lets go ahead and create the following file: 

```
/modules/custom/role_notices/role_notices.module
```

Alright, let keep moving...

### 4.1 Defining a display field

In order to properly integrate with the orderable fields screens in Drupal we need implement the *hook_entity_extra_field_info* hook. Through this hook we can add a display field to the **manage display** screen for the user entity. Lets see what this looks like: 

```php
/**
 * Implements hook_entity_extra_field_info().
 *
 * Add the our notices as a display field on the user.
 *
 * @see role_notices_user_view()
 */
function role_notices_entity_extra_field_info() {
  $fields['user']['user']['display']['role_notices'] = array(
    'label' => t('Notices'),
    'description' => t('Notices for the user.'),
    'weight' => 10,
  );

  return $fields;
}
```

Simply put this hook returns an array that contains information about the field we are defining. This code lives right in the .module file itself. 

Notice that this hook does not define the contents of the field, it just defines its existence. This allows us to order this field on the manage display screen just as we can with any field in the Field UI. The next hook will actually do the work of populating the notices.


### 4.2 Populating our display field with notices

The next hook we will need to implement will enable us to populate the display field we defined in the last section. We will accomplish this by leveraging the *hook_ENTITY_TYPE_view* hook. In our case, since we are displaying this on the user entity, *hook_user_view*.

Lets take a look at the code here: 

```php
/**
 * Implements hook_ENTITY_TYPE_view().
 *
 * For USER entity type.
 * We use this hook to render our notices on the user view.
 */
function role_notices_user_view(array &$build, UserInterface $account, EntityViewDisplayInterface $display) {
  /** @var Drupal\Core\Session\AccountProxy $logged_in_user */
  $logged_in_user = \Drupal::service('current_user');
  if ($account->id() == $logged_in_user->id()) {
    if ($display->getComponent('role_notices')) {
      $notices_manager = \Drupal::service('role_notices.notice_manager');
      $notices = $notices_manager->getUserNotices();
      $build['role_notices'] = array(
        '#type' => 'item',
        '#markup' => '<h4 class="label">' . t('Role Notices') . '</h4>',
      );

      $build['role_notices']['notices'] = array(
        '#theme' => 'item_list',
        '#items' => $notices,
        '#cache' => array(
          /*
           * Add cache tags so that this user view will be rebuilt when one of
           * the notices changes.
           *
           * @see Drupal\role_notices\NoticesManager::setAllNotices();
           */

          'tags' => $notices_manager->getRenderTags(array_keys($notices)),
        ),
      );
    }
  }
}
```
Lets talk a bit about what this hook is doing. Some really interesting happenings here. You will notices that we are leveraging our *NoticesManager* service to perform various tasks with our notices. We are accessing this service in a slightly different way then how we saw with our Form. We are using the global *\Drupal* object to directly access the service container and retrieve an instance of our service. 

You can think of this *\Drupal* object as a bridge between procedural land and OO land. 

You will also notices we are using this object to retrieve the current user. 

**Ok so what are we missing here?**

Well do to the fact that we are using the types *UserInterface* and *EntityViewDisplayInterface* as parameters to this hook we do need to declare they use at the top of this file. If we did not we would need to decalare its absoluate namespace in the parameter list, not cool. Lets go ahead and add this code to the top of our file: 

```php
<?php
/**
 * @file
 * Global function code file for role_notices module.
 *
 * This files contains our implementation of Drupal hooks.
 * Most of our module code will be in the /src directory.
 */

use Drupal\Core\Entity\Display\EntityViewDisplayInterface;
use Drupal\user\UserInterface;
```

Excellent! Good work team!! We now have a working module that fits the goals we defined. 

Who wants more? If so, lets dive into a little bonus section... and define a block.

[Back to top](#top)

## 5.0 Bonus: Defining a block

Another large change to our Drupal development lives entails the idea of a plugin. Plugins are small pieces of functionality that are swappable. 

**What does that mean?**

Well think of things like *field widgets* and *image styles*. Plugins implement different behaviors with a common interface. Unlike a service where the behavior is the same but the implementation may be different. Think about our NoticeManager service but using the Config system as opposed to the State API. 

In Drupal 8 [Blocks are now plugins](https://www.drupal.org/node/1880620). Bye bye *hook_block_info* and the like. 

### 5.1 Where plugins live

Plugins, which include our blocks will live in the `src/Plugin` directory of our module. Blocks will live in `src/Plugin/Block`. Lets go ahead and create the following file: 

```
/modules/custom/role_notices/src/Plugin/Block/RoleNoticesBlock.php
```

Following our pattern of: 

1. Build a tool. 
2. Tell Drupal about it. 

Blocks (and all plugins) follow the same pattern with a big different to how we accomplish the second part. To tell Drupal about our plugin we use annonations. 

Lets dive into our block...

### 5.2 Extending BlockBase

Lets go ahead build our block with the following code: 

```php
<?php

/**
 * @file
 * Contains Drupal\role_notices\Plugin\Block\RoleNoticesBlock.
 *
 * This is Plugin of the Block type.
 * Drupal finds out about this block via annotations
 *
 * If coming from Drupal 7 the annotations replace hook_block_info.
 * Build function replaces hook_block_view
 *
 * *** IMPORTANCE OF AN IDE ***
 * In this class we will implement the interface: ContainerFactoryPluginInterface
 * To do so we must implement the method "create".
 * If we don't do so this will be a fatal PHP error.
 *
 * If you are using a good IDE it will notify you are this error in your
 * workspace as you work.
 *
 * If you are using an IDE comment out the "create" function to see how your
 * IDE notifies you of this error.
 *
 * Also because we are extending the abstract class BlockBase which implements
 * BlockPluginInterface we must also implement the "build" method from
 * BlockPluginInterface.
 */

namespace Drupal\role_notices\Plugin\Block;


use Drupal\Core\Block\BlockBase;
use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
use Drupal\Core\Session\AccountInterface;
use Drupal\role_notices\NoticesManager;
use Drupal\Core\Access\AccessResult;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Provides a "Notices" block.
 *
 * Following part of comments is an annotation
 *
 * @link https://drupal.org/node/1882526
 *
 * @Block(
 *   id = "role_notices",
 *   admin_label = @Translation("Role Notices")
 * )
 */
class RoleNoticesBlock extends BlockBase implements ContainerFactoryPluginInterface {
  /**
   * The Notices Manager service.
   *
   * @var \Drupal\role_notices\NoticesManager
   */
  protected $noticesManager;

  /**
   * Constructs a RNoticesBlock object.
   *
   * @param array $configuration
   *   A configuration array containing information about the plugin instance.
   * @param string $plugin_id
   *   The plugin ID for the plugin instance.
   * @param mixed $plugin_definition
   *   The plugin implementation definition.
   * @param \Drupal\role_notices\NoticesManager $notices_manager
   *   The Role Notices NoticesManager.
   */
  public function __construct(array $configuration, $plugin_id, $plugin_definition, NoticesManager $notices_manager) {
    parent::__construct($configuration, $plugin_id, $plugin_definition);
    $this->noticesManager = $notices_manager;
  }

  /**
   * Creates an instance of the plugin.
   *
   * We must implement this because our class implements:
   *  \Drupal\Core\Plugin\ContainerFactoryPluginInterface.
   *
   * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
   *   The container to pull out services used in the plugin.
   * @param array $configuration
   *   A configuration array containing information about the plugin instance.
   * @param string $plugin_id
   *   The plugin ID for the plugin instance.
   * @param mixed $plugin_definition
   *   The plugin implementation definition.
   *
   * @return static
   *   Returns an instance of this plugin.
   */
  public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
    return new static(
      $configuration,
      $plugin_id,
      $plugin_definition,
      $container->get('role_notices.notice_manager')
    );
  }

  /**
   * Overrides \Drupal\block\BlockBase::blockAccess().
   */
  protected function blockAccess(AccountInterface $account) {
    return AccessResult::allowedIf($account->isAuthenticated());
  }

  /**
   * {@inheritdoc}
   */
  public function build() {
    /*
     * Without dependency injection, we could have accessed the NoticesManager
     * service directly using:
     * @code
     * return array(
     *  '#theme' => 'item_list',
     *  '#items' => \Drupal::service('role_notices.notice_manager')->getUserNotices(),
     * );
     * @endcode
     *
     */
    return array(
      '#theme' => 'item_list',
      '#items' => $this->noticesManager->getUserNotices(),
    );
  }

}
```

Phew that was alot of code! Lets bring this into our project and discuss what is going on here!

[Back to top](#top) 

## 6.0 Wrapping up
Ok awesome, we covered a lot of concepts in this exercise. As you can see our DX has changed significantly in this new version of Drupal.

### 6.1 Review core concepts
So what did we learn today? Lets summarize: 

1. The basic structure and layout of a Drupal 8 module. 
2. The idea of a ***Service*** and how to inject them into various components. 
3. We illustrated how to develop Forms in Drupal 8.
4. We learned how to define a Route and connect this to our form. 
5. We saw how we will still use hooks and how to define a field on the user entity. 
6. Lastly, we illustrated the Plugin system by defining our own block. 

Wow! That is a lot for one day. Good job!

### 6.2 Further Exploration
Even with all we did today... there is still so much to learn. It never stops, its what makes this work exciting! We have a resources page available [here](https://github.com/Drupal-8-Study-Group/2016-april-d8-training-exercise/blob/master/resources.md) chock full of links for further exploration. 

Happy building!

[Back to top](#top) 
  




