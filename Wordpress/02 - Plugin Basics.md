# Plugin Basics

## Getting Started

At its simplest, a WordPress plugin is a PHP file with a WordPress plugin header comment. It’s highly recommended that you create a directory to hold your plugin so that all of your plugin’s files are neatly organized in one place.

To get started creating a new plugin, follow the steps below.

- Navigate to your WordPress installation’s wp-content directory.
- Open the plugins directory.
- Create a new directory and name it after your plugin (e.g. plugin-name).
- Open your new plugin’s directory.
- Create a new PHP file (it’s also good to name this file after your plugin, e.g. plugin-name.php).
- Here’s what the process looks like on the Unix command line:

```bash
wordpress$ cd wp-content
wp-content$ cd plugins
plugins$ mkdir plugin-name
plugins$ cd plugin-name
plugin-name$ vi plugin-name.php
```

In the example above, “vi” is the name of the text editor. Use whichever editor that is comfortable for you.

Now that you’re editing your new plugin’s PHP file, you’ll need to add a plugin header comment.  This is a specially formatted PHP block comment that contains metadata about your plugin, such as its name and author.  At the very least, the plugin header comment must contain the name of your plugin.  Only one file in your plugin’s folder should have the header comment—if your plugin has multiple PHP files, only one of those files should have the comment.

```php
<?php
/*
Plugin Name: YOUR PLUGIN NAME
*/
```

After you save the file, you should be able to see your plugin listed in your WordPress site. Log in to your WordPress site, and click Plugins on the left navigation pane of your WordPress Admin. This page displays a listing of all the plugins your WordPress site has. Your new plugin should now be in that list!

## Hooks: Actions and Filters

WordPress hooks allow you to tap into WordPress at specific points to change how WordPress behaves without editing any core files.

There are two types of hooks within WordPress: actions and filters. Actions allow you to add or change WordPress functionality, while filters allow you to alter content as it is loaded and displayed to the website user.

Hooks are not just for plugin developers; hooks are used extensively to provide default functionality by WordPress core itself. Other hooks are unused place holders that are simply available for you to tap into when you need to alter how WordPress works. This is what makes WordPress so flexible.

### Basic Hooks

The 3 basic hooks you’ll need when creating a plugin are the register_activation_hook(), register_deactivation_hook() and the register_uninstall_hook().

The activation hook is run when you activate your plugin. You would use this to provide a function to set up your plugin — for example, creating some default settings in the options table.

The deactivation hook is run when you deactivate your plugin. You would use this to provide a function that clears any temporary data stores by your plugin.

These uninstall methods are used to clean up after your plugin is deleted using the WordPress Admin. You would use this to delete all data created by your plugin, such as any options that were added to the options table.

### Removing Hooks

You can also use invoke remove_action() to remove a function that was defined earlier. For example, if your plugin is an add-on to another plugin, you can use remove_action() with the same function callback that was added by the previous plugin with add_action(). The priority of actions is important in these situations, as remove_action() would need to run after the initial add_action().

You should be careful when removing an action from a hook, as well as when altering priorities, because it can be difficult to see how these changes will affect other interactions with the same hook. We highly recommend testing frequently.

You can learn more about creating hooks and interacting with them in the Hooks section of this handbook.

## WordPress APIs

Did you know that WordPress provides a number of Application Programming Interfaces (APIs)? These APIs can greatly simplify the code you need to write in your plugins. You don’t want to reinvent the wheel, especially when so many people have done a lot of the work and testing for you.

The most common one is the Options API, which makes it easy to store data in the database for your plugin. If you’re thinking of using cURL in your plugin, the HTTP API might be of interest to you.

Since we’re talking about plugins, you’ll want to study the Plugin API. It has a variety of functions that will assist you in developing plugins.

## How WordPress Loads Plugins

When WordPress loads the list of installed plugins on the Plugins page of the WordPress Admin, it searches through the plugins folder (and its sub-folders) to find PHP files with WordPress plugin header comments. If your entire plugin consists of just a single PHP file, like Hello Dolly, the file could be located directly inside the root of the plugins folder. But more commonly, plugin files will reside in their own folder, named after the plugin.

## Sharing your Plugin

Sometimes a plugin you create is just for your site. But many people like to share their plugins with the rest of the WordPress community. Before sharing your plugin, one thing you need to do is choose a license. This lets the user of your plugin know how they are allowed to use your code. To maintain compatibility with WordPress core, it is recommended that you pick a license that works with GNU General Public License (GPLv2+).

## Header Requirements

As described in Getting Started, the header comment is what tells WordPress that a file is a plugin.

At a minimum, a header comment must contain the Plugin Name, but several pieces can – and usually should – be included:

Plugin Name: (required) The name of your plugin, which will be displayed in the Plugins list in the WordPress Admin.
Plugin URI: The home page of the plugin, which should be a unique URL, preferably on your own website. This must be unique to your plugin. You cannot use a WordPress.org URL here.
Description: A short description of the plugin, as displayed in the Plugins section in the WordPress Admin. Keep this description to fewer than 140 characters.
Version: The current version number of the plugin, such as 1.0 or 1.0.3.
Alert: When assigning a version number to your project, keep in mind that WordPress uses the PHP version_compare() function to compare plugin version numbers. Therefore, before you release a new version of your plugin, you should make sure that this PHP function considers the new version to be “greater” than the old one.  For example, 1.02 is actually greater than 1.1.

Author: The name of the plugin author. Multiple authors may be listed using commas.
Author URI: The author’s website or profile on another website, such as WordPress.org.
License: The short name (slug) of the plugin’s license (e.g. GPL2). More information about licensing can be found in the WordPress.org guidelines.
License URI: A link to the full text of the license (e.g. https://www.gnu.org/licenses/gpl-2.0.html).
Text Domain: The gettext text domain of the plugin. More information can be found in the Text Domain section of the How to Internationalize your Plugin page.
Domain Path: The domain path let WordPress know where to find the translations. More information can be found in the Domain Path section of the How to Internationalize your Plugin page.
A valid PHP file with a header comment might look like this:

```php
<?php
/*
Plugin Name:  WordPress.org Plugin
Plugin URI:   https://developer.wordpress.org/plugins/the-basics/
Description:  Basic WordPress Plugin Header Comment
Version:      20160911
Author:       WordPress.org
Author URI:   https://developer.wordpress.org/
License:      GPL2
License URI:  https://www.gnu.org/licenses/gpl-2.0.html
Text Domain:  wporg
Domain Path:  /languages
*/
```

## Including a Software License

Most WordPress plugins are released under the GPL, which is the same license that WordPress itself uses. However, there are other options available. It is always best to clearly indicate the license your plugin uses.

In the Header Requirements section, we briefly mentioned how you can indicate your plugin’s license within the plugin header comment. Another common, and encouraged, practice is to place a license block comment near the top of your main plugin file (the same one that has the plugin header comment).

This license block comment usually looks something like this:

```php
<?php
/*
{Plugin Name} is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 2 of the License, or
any later version.

{Plugin Name} is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with {Plugin Name}. If not, see {URI to Plugin License}.
*/
```

When combined with the plugin header comment:

```php
<?php
/*
Plugin Name: WordPress.org Plugin
Plugin URI:  https://developer.wordpress.org/plugins/the-basics/
Description: Basic WordPress Plugin Header Comment
Version:     20160911
Author:      WordPress.org
Author URI:  https://developer.wordpress.org/
Text Domain: wporg
Domain Path: /languages
License:     GPL2

{Plugin Name} is free software: you can redistribute it and/or modify
```

## Activation / Deactivation Hooks

Activation and deactivation hooks provide ways to perform actions when plugins are activated or deactivated.

On activation, plugins can run a routine to add rewrite rules, add custom database tables, or set default option values.

On deactivation, plugins can run a routine to remove temporary data such as cache and temp files and directories.

Alert: The deactivation hook is sometimes confused with the uninstall hook. The uninstall hook is best suited to delete all data permanently such as deleting plugin options and custom tables, etc.

### Activation

To set up an activation hook, use the register_activation_hook() function:

```php
<?php
register_activation_hook( __FILE__, 'pluginprefix_function_to_run' );
```

### Deactivation

To set up a deactivation hook, use the register_deactivation_hook() function:

```php
register_deactivation_hook( __FILE__, 'pluginprefix_function_to_run' );
```

The first parameter in each of these functions refers to your main plugin file, which is the file in which you have placed the plugin header comment. Usually these two functions will be triggered from within the main plugin file; however, if the functions are placed in any other file, you must update the first parameter to correctly point to the main plugin file.

### Example

One of the most common uses for an activation hook is to refresh WordPress permalinks when a plugin registers a custom post type. This gets rid of the nasty 404 errors.

Let’s look at an example of how to do this:

```php
function pluginprefix_setup_post_type() {
    // register the "book" custom post type
    register_post_type( 'book', ['public' => 'true'] );
}
add_action( 'init', 'pluginprefix_setup_post_type' );

function pluginprefix_install() {
    // trigger our function that registers the custom post type
    pluginprefix_setup_post_type();

    // clear the permalinks after the post type has been registered
    flush_rewrite_rules();
}
register_activation_hook( __FILE__, 'pluginprefix_install' );
```

If you are unfamiliar with registering custom post types, don’t worry – this will be covered later. This example is used simply because it’s very common.

Using the example from above, the following is how to reverse this process and deactivate a plugin:

```php
function pluginprefix_deactivation() {
    // unregister the post type, so the rules are no longer in memory
    unregister_post_type( 'book' );
    // clear the permalinks to remove our post type's rules from the database
    flush_rewrite_rules();
}
register_deactivation_hook( __FILE__, 'pluginprefix_deactivation' );
```

For further information regarding activation and deactivation hooks, here are some excellent resources:

register_activation_hook() in the WordPress function reference.
register_deactivation_hook() in the WordPress function reference.

## Uninstall Methods

Your plugin may need to do some clean-up when it is uninstalled from a site.

A plugin is considered uninstalled if a user has deactivated the plugin, and then clicks the delete link within the WordPress Admin.

When your plugin is uninstalled, you’ll want to clear out any plugin options and/or settings specific to to the plugin, and/or other database entities such as tables.

Less experienced developers sometimes make the mistake of using the deactivation hook for this purpose.

This table illustrates the differences between deactivation and uninstall.

| Scenario                                     | Deactivation Hook | Uninstall Hook|
|:---------------------------------------------|:-----------------:|:-------------:|
| Flush Cache/Temp                             | Yes               | No            |
| Flush Permalinks                             | Yes               | No            |
| Remove Options from {$wpdb->prefix}_options  | No                | Yes           |
| Remove Tables from wpdb                      | No                | Yes           |

### Method 1: register_uninstall_hook

To set up an uninstall hook, use the register_uninstall_hook() function:

```php
register_uninstall_hook(__FILE__, 'pluginprefix_function_to_run');
```

### Method 2: uninstall.php

To use this method you need to create an uninstall.php file inside the root folder of your plugin. This magic file is run automatically when the users deletes the plugin.

For example: /plugin-name/uninstall.php

Alert: When using uninstall.php, before executing, the plugin should always check for the constant WP_UNINSTALL_PLUGIN to prevent direct access.

The constant will be defined by WordPress during the uninstall.php invocation.

The constant is NOT defined when uninstall is performed by register_uninstall_hook().

Here is an example deleting option entries and dropping a database table:

```php
// if uninstall.php is not called by WordPress, die
if (!defined('WP_UNINSTALL_PLUGIN')) {
    die;
}

$option_name = 'wporg_option';

delete_option($option_name);

// for site options in Multisite
delete_site_option($option_name);

// drop a custom database table
global $wpdb;
$wpdb->query("DROP TABLE IF EXISTS {$wpdb->prefix}mytable");
```

In Multisite, looping through all blogs to delete options can be very resource intensive.

## Best Practices

Here are some best practices to help organize your code so it works well alongside WordPress core and other WordPress plugins.

### Avoid Naming Collisions

A naming collision happens when your plugin is using the same name for a variable, function or a class as another plugin.

Luckily, you can avoid naming collisions by using the methods below.

#### Procedural

By default, all variables, functions and classes are defined in the global namespace, which means that it is possible for your plugin to override variables, functions and classes set by another plugin and vice-versa. Variables that are defined inside of functions or classes are not affected by this.

##### Prefix Everything

All variables, functions and classes should be prefixed with a unique identifier. Prefixes prevent other plugins from overwriting your variables and accidentally calling your functions and classes. It will also prevent you from doing the same.

##### Check for Existing Implementations

PHP provides a number of functions to verify existence of variables, functions, classes and constants. All of these will return true if the entity exists.

- Variables: isset() (includes arrays, objects, etc.)
- Functions: function_exists()
- Classes: class_exists()
- Constants: defined()

Example :

```php
<?php
//Create a function called "wporg_init" if it doesn't already exist
if ( !function_exists( 'wporg_init' ) ) {
    function wporg_init() {
        register_setting( 'wporg_settings', 'wporg_option_foo' );
    }
}
 
//Create a function called "wporg_get_foo" if it doesn't already exist
if ( !function_exists( 'wporg_get_foo' ) ) {
    function wporg_get_foo() {
        return get_option( 'wporg_option_foo' );
    }
}
```

#### OOP

An easier way to tackle the naming collision problem is to use a class for the code of your plugin.

You will still need to take care of checking whether the name of the class you want is already taken but the rest will be taken care of by PHP.

Example :

```php
<?php
if ( !class_exists( 'WPOrg_Plugin' ) ) {
    class WPOrg_Plugin
    {
        public static function init() {
            register_setting( 'wporg_settings', 'wporg_option_foo' );
        }

        public static function get_foo() {
            return get_option( 'wporg_option_foo' );
        }
    }

    WPOrg_Plugin::init();
    WPOrg_Plugin::get_foo();
}
```

#### File Organization

The root level of your plugin directory should contain your plugin-name.php file and, optionally, your uninstall.php file. All other files should be organized into sub folders whenever possible.

##### Folder Structure

A clear folder structure helps you and others working on your plugin keep similar files together.

Here’s a sample folder structure for reference:

/plugin-name
     plugin-name.php
     uninstall.php
     /languages
     /includes
     /admin
          /js
          /css
          /images
     /public
          /js
          /css
          /images

#### Plugin Architecture

The architecture, or code organization, you choose for your plugin will likely depend on the size of your plugin.

For small, single-purpose plugins that have limited interaction with WordPress core, themes or other plugins, there’s little benefit in engineering complex classes; unless you know the plugin is going to expand greatly later on.

For large plugins with lots of code, start off with classes in mind. Separate style and scripts files, and even build-related files. This will help code organization and long-term maintenance of the plugin.

##### Conditional Loading

It’s helpful to separate your admin code from the public code. Use the conditional is_admin().

For example:

```php
<?php
if ( is_admin() ) {
    // we are in admin mode
    require_once( dirname( __FILE__ ) . '/admin/plugin-name-admin.php' );
}
```

##### Architecture Patterns

While there are a number of possible architecture patterns, they can broadly be grouped into three variations:

- Single plugin file, containing functions
- Single plugin file, containing a class, instantiated object and optionally functions
- Main plugin file, then one or more class files

##### Architecture Patterns Explained

Specific implementations of the more complex of the above code organizations have already been written up as tutorials and slides:

- Slash – Singletons, Loaders, Actions, Screens, Handlers
- Implementing the MVC Pattern in WordPress Plugins

#### Boilerplate Starting Points

Instead of starting from scratch for each new plugin you write, you may want to start with a boilerplate. One advantage of using a boilerplate is to have consistency among your own plugins. Boilerplates also make it easier for other people to contribute to your code if you use a boilerplate they are already familiar with.

These also serve as further examples of different yet comparable architectures.

- WordPress Plugin Boilerplate: A foundation for WordPress Plugin Development that aims to provide a clear and consistent guide for building your plugins.
- WordPress Plugin Bootstrap: Basic bootstrap to develop WordPress plugins using Grunt, Compass, GIT, and SVN.
- WP Skeleton Plugin: Skeleton plugin that focuses on unit tests and use of composer for development.
- General search for WordPress plugin boilerplates on GitHub

Of course, you could take different aspects of these and others to create your own custom boilerplate.