# Introduction

## About the WordPress REST API

WordPress is moving towards becoming a fully-fledged application framework, and we needed new APIs. This project was born to create an easy-to-use, easy-to-understand and well-tested framework for creating these APIs, plus creating APIs for core.

The WordPress REST API provides an easy-to-use set of HTTP endpoints that let you access your site’s data in simple JSON format, including users, posts, taxonomies and more. Retrieving or updating data is as simple as sending a HTTP request.

Want to get your site’s posts? Simply send a GET request to /wp-json/wp/v2/posts. Update user with ID 4? Send a POST request to /wp-json/wp/v2/users/4. Get all posts with the search term “awesome”? GET /wp-json/wp/v2/posts?search=awesome. It’s that easy.

The API exposes a simple yet powerful interface to WP Query, the posts API, post meta API, users API, revisions API and many more. Chances are, if you can do it with WordPress, WP API will let you do it.