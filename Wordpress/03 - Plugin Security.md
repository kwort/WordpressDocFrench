# Plugin Security

Congratulations, your code works! But is it safe? How will the plugin protect your users if their site gets hacked? The best plugins in the WordPress.org directory keep their users’ information safe.

Please keep in mind that your code may be running across hundreds, perhaps even millions, of websites, so security is of the utmost importance.

In this chapter we will cover how to check user capabilities, validate and sanitize input, sanitize output and create and validate nonces.

## Quick Reference

See the complete example of security best practices for WordPress plugins and themes.

## External Resources

How to fix the intentionally vulnerable plugin by Jon Cave
Theme and Plugin Security presentation by Mark Jaquith

## Checking User Capabilities

If your plugin allows users to submit data—be it on the Admin or the Public side—it should check for User Capabilities.

### User Roles and Capabilities

The most important step in creating an efficient security layer is having a user permission system in place. WordPress provides this in the form of User Roles and Capabilities.

Every user logged into WordPress is automatically assigned specific User capabilities depending on their User role.

User roles is just a fancy way of saying which group the user belongs to. Each group has a specific set of predefined capabilities.

For example, the main user of your website will have the User role of an Administrator while other users might have roles like Editor or Author. You could have more than one user assigned to a role, i.e. there might be two Administrators for a website.

User capabilities are the specific permissions that you assign to each user or to a User role.

For example, Administrators have the “manage_options” capability which allows them to view, edit and save options for the website. Editors on the other hand lack this capability which will prevent them from interacting with options.

These capabilities are then checked at various points within the Admin. Depending on the capabilities assigned to a role; menus, functionality, and other aspects of the WordPress experience may be added or removed.

As you build a plugin, make sure to run your code only when the current user has the necessary capabilities.

#### Hierarchy

The higher the user role, the more capabilities the user has. Each user role inherits the previous roles in the hierarchy.

For example, the “Administrator”, which is the highest user role on a single site installation, inherits the following roles and their capabilities: “Subscriber”, “Contributor”, “Author” and “Editor”.

### Checking User Capabilities Examples

#### No Restrictions

The example below creates a link on the frontend which gives the ability to trash posts. Because this code does not check user capabilities, it allows any visitor to the site to trash posts!

```php
<?php
/**
 * generate a Delete link based on the homepage url
 */
function wporg_generate_delete_link($content)
{
    // run only for single post page
    if (is_single() &amp;&amp; in_the_loop() &amp;&amp; is_main_query()) {
        // add query arguments: action, post
        $url = add_query_arg(
            [
                'action' => 'wporg_frontend_delete',
                'post'   => get_the_ID(),
            ],
            home_url()
        );
        return $content . ' <a href="' . esc_url($url) . '">' . esc_html__('Delete Post', 'wporg') . '</a>;';
    }
    return null;
}

/**
 * request handler
 */
function wporg_delete_post()
{
    if (isset($_GET['action']) &amp;&amp; $_GET['action'] === 'wporg_frontend_delete') {

        // verify we have a post id
        $post_id = (isset($_GET['post'])) ? ($_GET['post']) : (null);

        // verify there is a post with such a number
        $post = get_post((int)$post_id);
        if (empty($post)) {
            return;
        }

        // delete the post
        wp_trash_post($post_id);

        // redirect to admin page
        $redirect = admin_url('edit.php');
        wp_safe_redirect($redirect);

        // we are done
        die;
    }
}

/**
 * add the delete link to the end of the post content
 */
add_filter('the_content', 'wporg_generate_delete_link');

/**
 * register our request handler with the init hook
 */
add_action('init', 'wporg_delete_post');

```

#### Restricted to a Specific Capability

The example above allows any visitor to the site to click on the “Delete” link and trash the post. However, we only want Editors and above to be able to click on the “Delete” link.

To accomplish this, we will check that the current user has the capability edit_others_posts, which only Editors or above would have:

```php
<?php
/**
 * generate a Delete link based on the homepage url
 */
function wporg_generate_delete_link($content)
{
    // run only for single post page
    if (is_single() &amp;&amp; in_the_loop() &amp;&amp; is_main_query()) {
        // add query arguments: action, post
        $url = add_query_arg(
            [
                'action' => 'wporg_frontend_delete',
                'post'   => get_the_ID(),
            ],
            home_url()
        );
        return $content . ' <a href="' . esc_url($url) . '">' . esc_html__('Delete Post', 'wporg') . '</a>;';
    }
    return null;
}
 
/**
 * request handler
 */
function wporg_delete_post()
{
    if (isset($_GET['action']) &amp;&amp; $_GET['action'] === 'wporg_frontend_delete') {

        // verify we have a post id
        $post_id = (isset($_GET['post'])) ? ($_GET['post']) : (null);

        // verify there is a post with such a number
        $post = get_post((int)$post_id);
        if (empty($post)) {
            return;
        }

        // delete the post
        wp_trash_post($post_id);

        // redirect to admin page
        $redirect = admin_url('edit.php');
        wp_safe_redirect($redirect);

        // we are done
        die;
    }
}

if (current_user_can('edit_others_posts')) {
    /**
     * add the delete link to the end of the post content
     */
    add_filter('the_content', 'wporg_generate_delete_link');

    /**
     * register our request handler with the init hook
     */
    add_action('init', 'wporg_delete_post');
}
```

## Data Validation

Data validation is the process of analyzing the data against a predefined pattern (or patterns) with a definitive result: valid or invalid.

Usually this applies to data coming from external sources such as user input and calls to web services via API.

Simple examples of data validation:

- Check that required fields have not been left blank
- Check that an entered phone number only contains numbers and punctuation
- Check that an entered postal code is a valid postal code
- Check that a quantity field is greater than 0

Data validation should be performed as early as possible. That means validating the data before performing any actions.

Note: Validation can be performed by using JavaScript on the front end and by using PHP on the back end.

### Validating the Data

There are at least three ways: built-in PHP functions, core WordPress functions, and custom functions you write.

#### Built-in PHP functions

Basic validation is doable using many built-in PHP functions, including these:

- isset() and empty() for checking whether a variable exists and isn’t blank
- mb_strlen() or strlen() for checking that a string has the expected number of characters
- preg_match(), strpos() for checking for occurrences of certain strings in other strings
- count() for checking how many items are in an array
- in_array() for checking whether something exists in an array

#### Core WordPress functions

WordPress provides many useful functions that help validate different kinds of data. Here are several examples:

- is_email() will validate whether an email address is valid.
- term_exists() checks whether a tag, category, or other taxonomy term exists.
- username_exists() checks if username exists.
- validate_file() will validate that an entered file path is a real path (but not whether the file exists).

Check the WordPress code reference for more functions like these.
Search for functions with names like these: *_exists(), *_validate(), and is_*(). Not all of these are validation functions, but many are helpful.

#### Custom PHP and JavaScript functions

You can write your own PHP and JavaScript functions and include them in your plugin. When writing a validation function, you’ll want to name it like a question (examples: is_phone, is_available, is_us_zipcode).

The function should return a boolean, either true or false, depending on whether the data is valid or not. This will allow using the function as a condition.

### Example 1

Let’s say you have an U.S. zip code input field that a user submits.

```php
<input id="wporg_zip_code" type="text" maxlength="10" name="wporg_zip_code">
```

The text field allows up to 10 characters of input with no limitations on the types of characters that can be used. Users could enter something valid like 1234567890 or something invalid (and evil) like eval().

The maxlength attribute on our input field is only enforced by the browser, so you still need to validate the length of the input on the server. If you don’t, an attacker could alter the maxlength value.

By using validation we can ensure we’re accepting only valid zip codes.

First you need to write a function to validate a U.S. zip codes:

```php
<?php
function is_us_zip_code($zip_code)
{
    // scenario 1: empty
    if (empty($zip_code)) {
        return false;
    }

    // scenario 2: more than 10 characters
    if (strlen(trim($zip_code)) > 10) {
        return false;
    }

    // scenario 3: incorrect format
    if (!preg_match('/^\d{5}(\-?\d{4})?$/', $zip_code)) {
        return false;
    }

    // passed successfully
    return true;
}
```

When processing the form, your code should check the wporg_zip_code field and perform the action based on the result:

if (isset($_POST['wporg_zip_code']) && is_us_zip_code($_POST['wporg_zip_code'])) {
    // your action
}

### Example 2

Say you’re going to query the database for some posts, and you want to give the user the ability to sort the query results.

This example code checks an incoming sort key (stored in the “orderby” input parameter) for validity by comparing it against an array of allowed sort keys using the built-in PHP function in_array. This prevents the user from passing in malicious data and potentially compromising the website.

Before checking the incoming sort key against the array, the key is passed into the built-in WordPress function sanitize_key. This function ensures, among other things, that the key is in lowercase (in_array performs a case-sensitive search).

Passing “true” into the third parameter of in_array enables strict type checking, which tells the function to not only compare values but value types as well. This allows the code to be certain that the incoming sort key is a string and not some other data type.

```php
<?php
$allowed_keys = ['author', 'post_author', 'date', 'post_date'];

$orderby = sanitize_key($_POST['orderby']);

if (in_array($orderby, $allowed_keys, true)) {
    // modify the query to sort by the orderby key
}
```

## Securing Input

Securing input is the process of sanitizing (cleaning, filtering) input data.

You use sanitizing when you don’t know what to expect or you don’t want to be strict with data validation.

Any time you’re accepting potentially unsafe data, it is important to validate or sanitize it.

### Sanitizing the Data

The easiest way to sanitize data is with built-in WordPress functions.

The sanitize_*() series of helper functions are super nice, as they ensure you’re ending up with safe data, and they require minimal effort on your part:

- sanitize_email()
- sanitize_file_name()
- sanitize_html_class()
- sanitize_key()
- sanitize_meta()
- sanitize_mime_type()
- sanitize_option()
- sanitize_sql_orderby()
- sanitize_text_field()
- sanitize_title()
- sanitize_title_for_query()
- sanitize_title_with_dashes()
- sanitize_user()
- esc_url_raw()
- wp_filter_post_kses()
- wp_filter_nohtml_kses()

### Example

Let’s say we have an input field named title.

```php
<input id="title" type="text" name="title">
```

You can sanitize the input data with the sanitize_text_field() function:

```php
$title = sanitize_text_field($_POST['title']);
update_post_meta($post->ID, 'title', $title);
```

Behind the scenes, sanitize_text_field() does the following:

- Checks for invalid UTF-8
- Converts single less-than characters (<) to entity
- Strips all tags
- Removes line breaks, tabs and extra white space
- Strips octets

## Securing Output

Securing output is the process of escaping output data.

Escaping means stripping out unwanted data, like malformed HTML or script tags.

Whenever you’re rendering data, make sure to properly escape it. Escaping output prevents XSS (Cross-site scripting) attacks.

Cross-site scripting (XSS) is a type of computer security vulnerability typically found in web applications. XSS enables attackers to inject client-side scripts into web pages viewed by other users. A cross-site scripting vulnerability may be used by attackers to bypass access controls such as the same-origin policy.

### Escaping

Escaping helps securing your data prior to rendering it for the end user. WordPress has a few helper functions you can use for most common scenarios.

- esc_html() – Use this function anytime an HTML element encloses a section of data being displayed.
- esc_url() – Use this function on all URLs, including those in the src and href attributes of an HTML element.
- esc_js()– Use this function for inline Javascript.
- esc_attr() – Use this function on everything else that’s printed into an HTML element’s attribute.

Alert: Most WordPress functions properly prepare data for output, so you don’t need to escape the data again. For example, you can safely call the_title() without escaping.

### Escaping with Localization

Rather than using echo to output data, it’s common to use the WordPress localization functions, such as _e() or __().

These functions simply wrap a localization function inside an escaping function:

```php
esc_html_e( 'Hello World', 'text_domain' );
// same as
echo esc_html( __( 'Hello World', 'text_domain' ) );
```

These helper functions combine localization and escaping:

- esc_html__()
- esc_html_e()
- esc_html_x()
- esc_attr__()
- esc_attr_e()
- esc_attr_x()

Custom Escaping

In the case that you need to escape your output in a specific way, the function wp_kses() (pronounced “kisses”) will come in handy.

This function makes sure that only the specified HTML elements, attributes, and attribute values will occur in your output, and normalizes HTML entities.

```php
$allowed_html = [
    'a'      => [
        'href'  => [],
        'title' => [],
    ],
    'br'     => [],
    'em'     => [],
    'strong' => [],
];
echo wp_kses( $custom_content, $allowed_html );
```

wp_kses_post() is a wrapper function for wp_kses where $allowed_html is a set of rules used by post content.

```php
echo wp_kses_post( $post_content );
```

## Nonces

Nonces are generated numbers used to verify origin and intent of requests for security purposes. Each nonce can only be used once.

If your plugin allows users to submit data; be it on the Admin or the Public side; you have to make sure that the user is who they say they are and that they have the necessary capability to perform the action. Doing both in tandem means that data is only changing when the user expects it to be changing.

### Using Nonces

Following our checking user capabilities example, the next step in user data submission security is using nonces.

The capability check ensures that only users who have permission to delete a post are able to delete a post. But what if someone were to trick you into clicking that link? You have the necessary capability, so you could unwittingly delete a post.

Nonces can be used to check that the current user actually intends to perform the action.

When you generate the delete link, you’ll want to use wp_create_nonce() function to add a nonce to the link, the argument passed to the function ensures that the nonce being created is unique to that particular action.

Then, when you’re processing a request to delete a link, you can check that the nonce is what you expect it to be.

For more information, Mark Jaquith’s post on WordPress nonces is a great resource.

### Complete Example

Complete example using capability checks, data validation, secure input, secure output and nonces:

```php
<?php
/**
 * generate a Delete link based on the homepage url
 */
function wporg_generate_delete_link($content)
{
    // run only for single post page
    if (is_single() && in_the_loop() && is_main_query()) {
        // add query arguments: action, post, nonce
        $url = add_query_arg(
            [
                'action' => 'wporg_frontend_delete',
                'post'   => get_the_ID(),
                'nonce'  => wp_create_nonce('wporg_frontend_delete'),
            ],
            home_url()
        );
        return $content . ' <a href="' . esc_url($url) . '">' . esc_html__('Delete Post', 'wporg') . '</a>';
    }
    return null;
}
 
/**
 * request handler
 */
function wporg_delete_post()
{
    if (
        isset($_GET['action']) &&
        isset($_GET['nonce']) &&
        $_GET['action'] === 'wporg_frontend_delete' &&
        wp_verify_nonce($_GET['nonce'], 'wporg_frontend_delete')
    ) {

        // verify we have a post id
        $post_id = (isset($_GET['post'])) ? ($_GET['post']) : (null);

        // verify there is a post with such a number
        $post = get_post((int)$post_id);
        if (empty($post)) {
            return;
        }

        // delete the post
        wp_trash_post($post_id);

        // redirect to admin page
        $redirect = admin_url('edit.php');
        wp_safe_redirect($redirect);

        // we are done
        die;
    }
}
 
if (current_user_can('edit_others_posts')) {
    /**
     * add the delete link to the end of the post content
     */
    add_filter('the_content', 'wporg_generate_delete_link');

    /**
     * register our request handler with the init hook
     */
    add_action('init', 'wporg_delete_post');
}
```