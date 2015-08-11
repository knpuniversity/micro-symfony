# Bootstrapping Micro-Symfony

Inspiration strikes! You've just come up with a crazy awesome idea - the kind of idea
that's sure to save the world and impress your friends all at once. Now all you need
to do is start coding.

Of course, Symfony - the fully-featured full-stack framework will be perfect for this.
Then again, what if we used Silex? The micro-framework approach might be a better
way to get started so the world can quickly find out what its been missing!

But Silex isn't Symfony: it lacks a lot of the tools. And if your project grows, it 
can't easily evolve into a Symfony app: they're just too different.

There's another option: use Symfony as a micro-framework, meaning with as few files,
directories and complications as possible, without sacrificing features. And since
no official Symfony micro-framework exists right now, let's create one.

## Composer Bootstrap

Start with a completely empty directory: so empty that we need to run `composer init`
to bootstrap a `composer.json` file:

```bash
composer init
```

Fill in the name - none of this matters too much in a private project. Ok, dependencies.
We only need one: `symfony/symfony`. But I also want one more library:
`sensio/framework-extra-bundle` - this is for annotation routing. If you don't want
that, you only need `symfony/symfony`.

Our blank project now has 1 file - `composer.json` - with just 2 dependencies:

[[[ code('a1b9631172') ]]]

Go back to run `composer install`:

```bash
composer install
```

Now our empty project has a `vendor/` directory.

## Bootstrapping Symfony

Now that we have Symfony how do we bootstrap the framework? We need two things: a 
kernel class and a front controller script that boots and executes it.

Start with the kernel: create a new `AppKernel.php` file at the root of your project.
You can call this file anything, but I want to stay consistent with normal Symfony
when possible. Make this extend Symfony's `Kernel` class. Since we're not in a namespaced
class, PhpStorm auto-completes the namespaces inline. Ew! Add a `use` statement instead:
let's keep things somewhat organized people:

[[[ code('77a76cbb05') ]]]

`Kernel` is an abstract class. Use cmd+n - or the menu option, Code->Generate - then 
"Implement Methods". Select the two methods at the bottom:

[[[ code('8b37173c93') ]]]

### registerBundles()

Get rid of the comments. So first, let's instantiate all the bundles we need.
In a normal, new, Symfony project, there are 8 or more bundles here by default. We
just need 3: a new `FrameworkBundle`, `TwigBundle` - this is used to render error
and exception pages, so you may want this, even if you're building an API. And since
I'll use annotations for my routing, use `SensioFrameworkExtraBundle`. If you like
YAML routing, you don't even need this last one. Return the `$bundles`:

[[[ code('d022898e8f') ]]]

### registerContainerConfiguration()

For the next method - move the inline namespace to be a `use` statement on top. When
the kernel boots, it needs to configure each bundle - it needs things like the database
password, where to log, etc. That's the purpose of `registerContainerConfiguration()`.

Use `$loader->load()` and point this to a new `config/config.yml` file:

[[[ code('299b7f2281') ]]]

And now create the `config` directory and that file so that it isn't pointing into
outerspace. Leave it blank for now.

## Front Controller: index.php

That's a functional kernel! We need a front controller that can instantiate and execute
it. Create a `web/` directory with an `index.php` inside. Symfony has two front controllers:
`app.php` and `app_dev.php`: we'll have just one because I want less less less. We're going
for the micro of micro!

In my browser, we're looking at the [symfony/symfony-standard](https://github.com/symfony/symfony-standard)
repository, because I want to steal some things. Open the `web/` directory and find
`app_dev.php`. Copy its contents into `index.php`. Now we're going to slim this
down a bit:

[[[ code('3f6c778ed1') ]]]

Delete the IP-checking stuff and uncomment out the `umask` - that makes dealing
with permissions easier. For the loader, require Composer's standard autoloader
in `vendor/autoload.php`. And `AppKernel` is *not* in an `app/` directory - so update
the path:

[[[ code('ac6cdf83a5') ]]]

When you create the kernel, you pass it an environment and whether or not we're in
`debug` mode: that controls whether errors are shown. We'll tackle these properly
soon, but right now, hardcode two new variables `$env = 'dev'` and `$debug = true`.
Pass these into `AppKernel`:

[[[ code('d1741b9b0b') ]]]

Oh, and put an `if` statement around the `Debug::enable()` line so it only runs in
debug mode. This wraps PHP's error handler and gives us better errors:

[[[ code('fa6dd1cf96') ]]]

And in the spirit of making everything as small as possible, remove the `loadClassCache()`
line: this adds a small performance boost. If you're feeling less micro, you can keep it.

## Testing it out

That's it for the front controller! Time to try things. Open up a new terminal tab,
move into the web directory, then start the built-in web server on port 8000:

```bash
cd web/
php -S localhost:8000
```

Let's try it - we're hoping to see a working Symfony 404 page, because we don't have
any routes yet. Okay, not working yet - but this isn't so bad: it's a Symfony error,
we're missing some `kernel.secret` parameter.

## Adding the Base Config

The reason is that we do need a *little* bit of configuration in `config.yml`. Add
a `framework` key with a `secret` under it that's set to anything:

[[[ code('be4c01c71e') ]]]

When we refresh now, it's a different error: something about a Twig template that's
not defined. These weird errors are due to some missing configuration. Here's the
minimum you need. Add a `router` key with a `resource` sub-key that points to a routing
file. Use `%kernel.root_dir%/config/routing.yml`. `%kernel.root_dir%` points to
wherever your kernel lives, which is the `app/` directory in normal Symfony, but
the root folder for us minimalists.

You also need a `templating` key with an `engines` sub-key set to `twig`:

[[[ code('47251ea6dc') ]]]

That's all you need to get things working. Oh, and don't forget to create
a `config/routing.yml` file - it can be blank for now:

[[[ code('a0f3be9a83') ]]]

Refresh! It lives! "No route found for GET /" - that's our working 404 page. We'll fix 
that missing CSS in a bit, but this *is* a functional Symfony project: it just doesn't
have any routes yet.

## The Symfony Plugin

One of the coolest things about using Symfony is the PhpStorm plugin for it, which we
cover in the [Lean and Mean dev with PHP Storm tutorial](https://knpuniversity.com/screencast/phpstorm). 

If you have it installed, search "symfony" in your settings to find it. Check "Enable" and
remove `app/` from all the configurable paths. Our project is smaller than normal:
instead of `app/cache` and `app/logs`, you just have `cache` and `logs` at the root.
Init a new `git` repository and add these to your `.gitignore` file along with `vendor/`:

[[[ code('b7f2c54bcd') ]]]

Count the files: other than composer stuff, we have 1, 2, 3, 4 files, and a functioning
Symfony framework project. #micro
