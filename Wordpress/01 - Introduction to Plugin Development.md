# Introduction to Plugin Development

Welcome to the Plugin Developer Handbook. Whether you’re writing your first plugin or your fiftieth, we hope this resource helps you write the best plugin possible.

The Plugin Developer Handbook covers a variety of topics — everything from what should be in the plugin header, to security best practices, to tools you can use to build your plugin. It’s also a work in progress — if you find something missing or incomplete, please edit and make it better.

There are three major components to WordPress:

core
themes
plugins
This handbook is about plugins and how they interact with WordPress. It will help you understand how they work and how to create your own.

## Why We Make Plugins

If there’s one cardinal rule in WordPress development, it’s this: Don’t touch WordPress core. This means that you don’t edit core WordPress files to add functionality to your site. This is because, when WordPress updates to a new version, it overwrites all the core files. Any functionality you want to add should therefore be added through plugins using approved WordPress APIs.

WordPress plugins can be as simple or as complicated as you need them to be, depending on what you want to do. The simplest plugin is a single PHP file. The Hello Dolly plugin is an example of such a plugin. The plugin PHP file just needs a Plugin Header, a couple of PHP functions, and some hooks to attach your functions to.

Plugins allow you to greatly extend the functionality of WordPress without touching WordPress core itself.

## What is a Plugin?

Plugins are packages of code that extend the core functionality of WordPress. WordPress plugins are made up of PHP code and other assets such as images, CSS, and JavaScript.

By making your own plugin you are extending WordPress, i.e. building additional functionality on top of what WordPress already offers. For example, you could write a plugin that displays links to the ten most recent posts on your site.

Or, using WordPress’ custom post types, you could write a plugin that creates a full-featured support ticketing system with email notifications, custom ticket statuses, and a client-facing portal. The possibilities are endless!

Most WordPress plugins are composed of many files, but a plugin really only needs one main file with a specifically formatted DocBlock in the header.

Hello Dolly, one of the first plugins, is only 82 lines long. Hello Dolly shows lyrics from the famous song in the WordPress admin. Some CSS is used in the PHP file to control how the lyric is styled.

As a WordPress.org plugin author, you have an amazing opportunity to create a plugin that will be installed, tinkered with, and loved by millions of WordPress users. All you need to do is turn your great idea into code. The Plugin Handbook is here to help you with that.