# UserFrosting v0.3.0

[![Join the chat at https://gitter.im/alexweissman/UserFrosting](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/alexweissman/UserFrosting?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## By [Alex Weissman](http://alexanderweissman.com)

Copyright (c) 2015, free to use in commercial software as per the [license](licenses/UserFrosting.md)

UserFrosting is a secure, modern user management system written in PHP and built on top of the [Slim Microframework](http://www.slimframework.com/) and the [Twig](http://twig.sensiolabs.org/) templating engine.

## Installation

UserFrosting comes with a built-in installer.  Assuming you have a working web server with PHP and MySQL installed, all you need to do is create a database and tell UserFrosting how to connect in the configuration file.  If you do not have a web server set up, we recommend [XAMPP](https://www.apachefriends.org/index.html).

### Step 1

Download the [latest version](https://github.com/alexweissman/UserFrosting/zipball/master) of UserFrosting, unzip/untar it, and place it in your document root.  The two directories that you need to worry about are `public` and `userfrosting`.  On a production server, you may wish to put the contents of `public` directly into the top-level public directory of your web hosting account, and put the `userfrosting` directory at the same level as the public directory.  So for example, if you FTP into your hosting account:

```
/                 // The root directory of your hosting account
|-- etc
|-- logs
|-- public_html      // Put the contents of 'public' in here
|   |
|   |-- css
|   |-- images
|   |-- js
|   |-- .htaccess
|   |-- index.php
|
|-- userfrosting    // Put the contents of 'userfrosting' in here
|   |-- auth
|   |-- controllers
|   |-- locale
|   |-- models
|   |-- plugins
|   |-- schema
|   |-- templates
|   |-- vendor
|   |-- composer.json
|   |-- composer.lock
|   |-- config-userfrosting.php
```

### Step 2

Specify your database information in `userfrosting/config-userfrosting.php`.  You will need to set the values of `db_host` (usually `localhost`), `db_name`, `db_user`, `db_pass`, and `db_prefix` (you can leave `db_prefix` as the default value if you wish).  

```
/********* DEVELOPMENT SETTINGS *********/
$app->configureMode('dev', function () use ($app) {
    $app->config([
        'log.enable' => true,
        'debug' => false,
        'base.path'     => __DIR__,            
        'templates.path' => __DIR__ . '/templates',
        'themes.path'    =>  __DIR__ . '/templates/themes',
        'schema.path' =>    __DIR__ . '/schema',
        'locales.path' =>   __DIR__ . '/locale',
        'log.path' =>   __DIR__ . '/log',
        'db'            =>  [
            'db_host'  => 'localhost',
            'db_name'  => 'userfrosting',
            'db_user'  => 'admin',
            'db_pass'  => 'password',
            'db_prefix'=> 'uf_'
        ],
    ...
```

If you frequently deploy and modify your code, you may wish to set up separate development and production configurations.  You will notice that immediately below this block of code is a nearly identical block, except that instead of `$app->configureMode('dev' ...` it says `$app->configureMode('production' ...`.  You can then easily switch between these two modes on line 13:

```
    $app = new \Slim\Slim([
        'view' =>           new \Slim\Views\Twig(),
        'mode' =>           'dev'   // Set to 'dev' or 'production'
    ]);
```

### Step 3

Navigate to the public directory (e.g. `http://localhost/userfrosting/public/`).  The installer should automatically come up and ask you to create the master account.  Choose a strong password for the master account if you are deploying production-level code.  The master user will have unrestricted permissions on your UserFrosting site.

## Screenshots

#### Login page
![Login page](/screenshots/login.png "Login page")
#### Dashboard (thanks to [Start Bootstrap](http://startbootstrap.com))
![Admin dashboard](/screenshots/dashboard.png "Admin dashboard")
#### User list page
![User list](/screenshots/users.png "User list page")
#### Create/update user dialog with validation
![Create/update user user](/screenshots/update_user.png "Create/update user dialog")
#### Group management
![Group management](/screenshots/groups.png "Group management page")
#### Site settings
![Site settings](/screenshots/site_settings.png "Site settings page")

## Mission Objectives

UserFrosting seeks to balance modern programming principles, like DRY and MVC, with a shallow learning curve for new developers.  Our goals are to:

- Create a fully-functioning user management script that can be set up in just a few minutes
- Make it easy for users to quickly adapt the code for their needs
- Introduce novice developers to best practices such as separation of concerns and DRY programming
- Introduce novice developers to modern constructs such as front-end controllers, RESTful URLs, namespacing, and object-oriented modeling
- Build on existing, widely used server- and client-side components
- Clean, consistent, and well-documented code

## What's new in 0.3.0

### Autoloading with Composer

UserFrosting builds on top of a number of existing components.  After all, why reinvent the wheel if there is already a well-documented, well-tested solution?  However, new versions of these *dependencies* are released all the time, and it can be difficult to keep up with changes.

To efficiently manage UserFrosting's dependencies, we use [Composer](https://getcomposer.org/).  Composer is a dependency management tool that you can install locally on your development workstation.  When run from the command line in your project directory, it consults a special schema file, [`composer.json`](userfrosting/composer.json), and downloads the dependencies defined in that file from a central repository called [Packagist](https://packagist.org/).  By default and by convention, each component is placed in a separate subdirectory of the `vendor` directory.  [UserFrosting's `vendor` directory](userfrosting/vendor) is located in the `userfrosting` directory.

Composer also **autoloads** the files and classes from these packages.  This means that instead of having a long list of `require` statements in the config file, we only need to include one file, `vendor/autoload.php`.  This file is automatically generated by Composer by scanning the package contents.  Composer can also autoload the files and classes that are specific to UserFrosting.  When you run `composer update`, Composer will scan the `auth`, `controllers`, and `models` directories for new files and classes.

#### Installing Composer

For your convenience, the latest versions of UserFrosting's dependencies are already included in this repository.  However, if you choose to use UserFrosting for your own project, you will likely want to do one or more of these things:

1. Install a package that isn't already included;
2. Update a package to a newer version;
3. Add your own files and class definitions to the core codebase.

In this case, you will need to install Composer.

To install composer on a Mac or other Unix-like operating system, visit [install it](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx).  It is recommended that you install it globally.

To add additional dependencies, you will need to modify the `userfrosting/composer.json` file.  After this, run `composer update` in the `userfrosting` subdirectory to install the new dependencies.

All dependencies are installed in `userfrosting/vendor`.  **Do not manually change the contents of this directory!**

Libraries which have been installed with composer are autoloaded, so there is no need to include individual files.  All you need is the `vendor/autoload.php` file, which is already included in `userfrosting/config-userfrosting.php`.  See the "Configuration" section for more information on the config file.

### Front Controllers and the Slim Microframework

If you're coming from a previous version of UserFrosting, you've probably that the flow of the code has changed substantially.  In particular, we now use a [**front controller pattern**](https://en.wikipedia.org/wiki/Front_Controller_pattern), also known as a URL router, which creates a layer of abstraction between the URL that you visit (for example, http://mysite.com/dashboard) and the code that gets run.

If you're new to PHP, you've probably been using the **one-url-one-file** scheme.  This means that, in the document root of the filesystem on your server (for example, `/~alexw/dev/htdocs/`), you create `.php` files that correspond to the URLs that users of your site can visit.  So, you might have a file `/~alexw/dev/htdocs/command-center.php`, and then you visit `http://localhost/command-center.php` to see the output of this script.

But here's the deal: there's no law set in stone that says it *has* to work this way.  When you visit a URL, all you're really doing is placing an HTTP `GET` **request** to a server (e.g., Apache).  The request is basically asking the server to generate the appropriate **response** to that request.  In the case of your typical home setup with Apache and PHP, the default behavior for a request is to look for a PHP script with the same name (`command-center.php`) in some preconfigured document root directory, run it, and send its output back as the response, where it is displayed in the client's browser.

However, it is possible to configure the server to interpret requests differently - this is known as **routing**.  Why would you want to do this?  Because it gives you more flexibility.  Let's say you want your site to have a URL like `http://mysite.com/blog/2015-06-01/1`, which points to the first page of your blog posts from June 1.  Without routing, you'd need to have actual subdirectories on your server's filesystem - `/~alexw/dev/htdocs/blog/2015-06-01/1.php`.

What's wrong with this?  Well, let's say you want the same blog post to also appear at other URLs, for example `http://mysite.com/blog/rants` and `http://mysite.com/blog/favorites/1`.  You'd need to have these subdirectories as well, and you'd have to create actual scripts that output the same content.  This becomes even more problematic if you want dynamically generated URLs, like `http://mysite.com/blog/words-from-a-database`.

On the other hand, with a front controller, you can link URLs to specific pieces of code without needing to create a separate file.

### How does UserFrosting make this happen?

UserFrosting uses the [Slim Framework](http://www.slimframework.com/) to make this work.  Here's how:

1.  When a user visits a URL such as `http://mysite.com/users/u/1`, they are actually seeing a [rewritten URL](https://en.wikipedia.org/wiki/Rewrite_engine).  In reality, every request is sent to `index.php`, with `users/u/1` sent as a request parameter.  In Apache, this is done with an `.htaccess` file (a preconfigured `.htaccess` file is included with UserFrosting).  Other web server technologies may use a different type of configuration file.
2. In `index.php`, a number of routes are defined, which tell it how to respond based on the request parameter.  Slim provides the framework for handling these routes.  For example:

```
$app->get('/users/u/1/?', function () use ($app) {
    echo "Hello I am user number 1";
});
```

generates the output for `http://mysite.com/users/u/1`.  `$app` is the Slim application (a global variable), and `get` tells us that we are dealing with a `GET` request (any time you navigate to a URL in your browser, you are submitting a `GET` request).

Another advantage is that routes can have variables in them.  For example:

```
$app->get('/users/u/:user_id/?', function ($user_id) use ($app) {
    echo "Hello I am user number $user_id";
});
```

Now we can visit any URL, for example `http://mysite.com/users/u/27`, and we will get the corresponding output "Hello I am user number 27."

## Libraries

- URL Routing and micro-framework: [Slim](http://www.slimframework.com/)
- Templating: [Twig](http://twig.sensiolabs.org/)

## Why UserFrosting?

This project grew out of a need for a simple user management system for my tutoring business, [Bloomington Tutors](http://bloomingtontutors.com).  I wanted something that I could develop rapidly and easily customize for the needs of my business.  Since my [prior web development experience](http://alexanderweissman.com/projects/) was in pure PHP, I decided to go with the PHP-based UserCake system.

Over time I modified and expanded the codebase, turning it into the UserFrosting project.  This latest version (0.3.0) represents a major break from the original architecture of UserCake.  We now use a fully object-oriented data model and a front controller for URL routing.

## Configuration


## Features



### Site Settings

```
class SiteSettings

$app->site = new SiteSettings();        // Loads all settings from the database on instantiation
echo $app->site->site_title;            // Print site title
$app->site->site_title = "Something Different";     // Change site title
$app->site->new_option = "Something";               // FAILS!  cannot add/remove settings in core context
$app->site->set("myPlugin", "setting1", "val");    // Create or update a setting called "setting1" in the "myPlugin" context, and set its value
$app->site->register("myPlugin", "setting1", "Ninjas?", "toggle", [0 => "off", 1 => "on"]);     // Register a setting with the Site Settings page, and the specified parameters.
$app->site->store();    // Save all settings in DB
```

### URL Scheme

```
GET  /users             // List users
GET  /users/u/1         // View info for user 1
POST /users             // Create a new user
POST /users/u/1         // Update info for user 1
POST /users/u/1/delete  // Delete user 1 (this is not RESTful, but many browsers still don't support DELETE)

GET /forms/users/u/1?mode="view"     // Get a form to view user info for user 1
GET /forms/users/u/1?mode="update"   // Get a form to update user info for user 1
GET /forms/users                     // Get a form to create a new user

```

### Sessions

UserFrosting will use native PHP sessions.  We could use Slim's [encrypted session cookies](http://docs.slimframework.com/#Cookie-Session-Store), but unfortunately they only allow a max of 4KB of data - too little for what a typical use case will require.

UF will keep everything that it needs in the `$_SESSION["userfrosting"]` key.  This includes the following:

- `$_SESSION["userfrosting"]["user"]`: A `User` object for the currently logged-in user.
- `$_SESSION["userfrosting"]["alerts"]`: A `MessageStream` object, that stores persistent messages.
- `$_SESSION["userfrosting"]["captcha"]`: The most recently generated captcha code, used to verify new account registration.

The old version of UF suffers from PHP's native sessions randomly expiring.  This may be an issue related to server configuration, rather than a problem with UF itself.  More research is needed.
http://board.phpbuilder.com/showthread.php?10313632-Sessions-randomly-dropped!
https://stackoverflow.com/questions/1327351/session-should-never-expire-by-itself
http://jaspan.com/improved_persistent_login_cookie_best_practice

It could also be due to issues with other PHP applications running on the same server: https://stackoverflow.com/questions/3476538/php-sessions-timing-out-too-quickly

### Authentication

UserFrosting 0.3.0 will use the same robust authentication system, with Blowfish password hashing.  Password resets will be done via a short-term expiring token.

We will model a "guest user", which basically means any user who is not logged in.  This means that we will no longer need to do a separate check to see if a user is logged in - the controller can simply check if a user is authorized, and by default, the guest user is not authorized to do anything.

### Authorization Hooks

UserFrosting will control access via **authorization hooks**, which represent a "checkpoint" in the codebase to determine whether or not a user is allowed to view or manipulate the model in some way.  Hooks are represented by a unique name.

The developer can then call the function `checkAccess` on a given hook at any place in the code where she wants to control access.  Think of them as the guards of the castle that is your website.  Hooks can be used to control access to entire pages (by calling them at the beginning of a route), or to control specific components and behaviors of your application.

For example, suppose we want to control whether or not someone is allowed to update a message on a message board.  Let's call our hook `updateMessage`.  Suppose we are processing a POST request that contains the updated contents of the message.  For the sake of example, we've just hardcoded the request data as `$post` (in reality, you'd probably get it from `$app->request->post()`, then do some sanitization, validation, etc).

```
$post = [
    "id"        => 42,
    "title"     => "Authorization control in UserFrosting",
    "content"   => "Everything you ever wanted to know!"
];

if ($app->user->checkAccess("updateMessage", ["message" => $post])){
    $message = MessageBoard::fetchMessage($post["id"]);
    $message->update($post);
} else {
    $ms->addMessage("danger", "The user does not have permission to update this post!");
    $app->halt(403);
}
```

So, where exactly do we decide who is authorized on the `updateMessage` hook?  In the database, of course!

We use two tables, `uf_authorize_user` and `uf_authorize_group`, which we will collectively refer to as the **access control list (ACL)**.  Note that our concept of "access control list" is far more sophisticated than the traditional meaning.  UserFrosting's ACL not only handles roles (which we call groups), making it more like RBAC, but it also allows for context-sensitive access control via a set of **conditions**.  Thus, UF provides for extremely powerful, fine-grained access control.  Rules like "allow users in group 'Tutor' to schedule sessions for students, but only if they are assigned to that student" can be defined with a single entry in the `uf_authorize_group` table.  **As far as we know, this is the only system that allows for fully programmatic, role- and context- based access control for users.**

The tables `uf_authorize_user` and `uf_authorize_group` will associate a user/group with hooks that they are authorized for, along with a set of conditions that must be satisfied.

| id  | group_id | hook | conditions |
| ------------- | ------------- | ------------- | ------------- |
| 1 | 1 | updateUser | equals(self.id,user.id)&&subset(user, ["display_name", "email"]) |
| 2 | 1 | updateMessage | hasMessage(self.id,message.id)&&subset(message, ["id", "title", "content", "subject"]) |

When `checkAccess("updateUser", $params)` is called, the authorization module performs the following steps:

1. Find an entry for the hook (e.g., `updateUser`) in the access control tables that either match the currently logged-in user directly, or one of the logged-in user's groups.
2. If the entry exists, check whether the conditions are satisfied (conditions can be joined together with the logical operators && and ||.)
  - A condition is checked by passing in the contents of `$params` to the `AccessCondition` function of the same name.
  - All `AccessCondition` functions also have access to the special `self` scope, which contains the information for the currently logged-in user, and the `route` scope, which contains the parameters of the current request route.
  - In this example, `equals` is an `AccessCondition` function that returns true if the two parameters are equal, and false otherwise.  In this case, we are checking to see if the currently logged-in user's (`self`) `id` matches the `id` of the user they are trying to update.  In other words, this checks that the user is only attempting to modify their own information.
3. If the conditions are met (such that the boolean string evaluates to `true`), then access is granted.
4. If the entry does not exist, or the conditions were not met, then access is denied.
5. There can only be one entry in the access control tables per group/hook pair or user/hook pair. 

#### Preset URI hooks:

- uri_home
- uri_dashboard
- uri_site_settings
- uri_slim_info
- uri_php_info
- uri_zerg
- uri_users
- uri_error_log

### Data Sanitization and Validation

UserFrosting uses the [Fortress](https://github.com/alexweissman/fortress) project to provide a schema-based system for sanitizing and validating user data.  This schema consists of a simple JSON file, with rules for how each user-submitted field should be processed.  The `HTTPRequestFortress` class handles backend sanitization and validation, while the `ClientSideValidator` class generates client-side validation rules compatible with the [FormValidation](http://formvalidation.io) Javascript plugin.

Sanitization should probably happen when data is used (i.e. displayed), rather than when input.  See http://lukeplant.me.uk/blog/posts/why-escape-on-input-is-a-bad-idea/.
So, it should go something like:
raw input -> validation -> database -> sanitization -> output

 
We need a better interface for modifying permissions:
https://github.com/alexweissman/UserFrosting/issues/127
 
### Theming

Themes allow custom css and layouts for different groups and users.  Twig templates, in essence, already support this via an elegant system of template directories.

Our theming system consists of a separate directory for each theme, which contains one or more HTML template files and a theme stylesheet, `css/theme.css`.  This stylesheet is imported into the public folder via a special route.

Theme directories are located in `userfrosting/templates/themes`.  The default theme is "default", and other themes work by overriding this content.  UF will by default look in the "default" theme for template files if if cannot find them in the current theme.

If you want to completely change the *content* of a page for a particular group, you should make a completely new page and then set permissions appropriately.  If you just want to change the *layout and style* of a page, then you should use a theme on top of an existing page.

### Plugins

We need a plugin system that is easily extendable, and exposes the Slim `$app` instance to the plugin developer.  It should also allow the developer to modify the user's environment.

### Alerts

UserFrosting pushes all alerts (warnings, errors, success messages) to a session `MessageStream` object.  This object can be accessed by calling `getAndClearMessages()` on the `alerts` member of the Slim app.  Thus, a typical way to fetch alerts on the server side would be:

```
$alerts = $app->alerts->getAndClearMessages();
```

Session alerts can be retrieved on the client side through the /alerts route.  Messages are sent back to the client in this manner, rather than directly through the HTTP response body, because in some cases we will want to persist messages across one or more requests.  For example, after an AJAX request, you may want to refresh the page **and then** display the alerts.  If the messages were directly part of the HTTP response from the AJAX request, they would be lost after the page refresh.

### Internationalization (i18n)

Internationalization will be handled essentially the same way that it was in UserCake - through an array that maps message ids to messages in a particular language.  In UserFrosting, this is handled through the static class `\Fortess\MessageTranslator`.  Also, UserFrosting will use named placeholders with the double-handlebar notation `{{user_name}}` instead of UserCake's old `%m1%` syntax.  Translation is performed using the static `translate` function:
`MessageTranslator::translate("MESSAGE_ID", [ "placeholder1" => "value1", "placeholder2" => "value2")]);`

So if `MESSAGE_ID` is defined as "This is the message, which references {{placeholder1}} and {{placeholder2}}.", the output will be:
"This is the message, which references value1 and value2."

Messages can be automatically translated and pushed to the message stream using `MessageStream`'s `addMessageTranslated` function:
`$ms->addMessageTranslated("info", "MESSAGE_ID", [ "placeholder1" => "value1", "placeholder2" => "value2")]);`

