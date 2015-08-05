# AppBundle, Routing and Annotations

Next, we need to build a route and create a page. But I want to do this with as few
files as possible.

In Symfony, your PHP code lives in a bundle. But actually, you don't *need* a bundle
at all: you could code entirely without one. And this could give us one less file.

In practice, *not* having a bundle complicates a few things. So, we *will* create
one here, but if you're curious about the approach, ask me, or read our
[AppBundle](http://knpuniversity.com/blog/AppBundle) blog post that talks about this.

## Creating AppBundle

Create a new directory called just `AppBundle` - not `src/AppBundle` - I want to
eliminate some directories! Put a new PHP class in there *also* called `AppBundle`
in an `AppBundle` namespace. Make this class extend `Bundle`:

[[[ code('dc461dbe64') ]]]

Activate this in `AppKernel` with new `AppBundle/AppBundle()`:

[[[ code('5fbe313125') ]]]

## Routing

Cool - that's the last time you'll need to do that. Now let's create a page. Open
`routing.yml`. I want to keep things as small as possible: minimizing code and especially
minimizing files. So I'll use annotation routing. If you prefer YAML, you can start
adding your routes here, point them to controller classes and be on your way.

For us, add an import: `resource: @AppBundle/Controller` with `type: annotation`:

[[[ code('3f75ab838c') ]]]

In `AppBundle`, create the `Controller` directory and a new class in there called...
how about... `MighyMouseController` after something else that's small, but can kick
butt:

[[[ code('') ]]]

Add `public function rescueAction()` and return a `new Response()`: "Here I come
to save the day!":

[[[ code('') ]]]

For routing, it's like normal: add the `use` statement for `Route`. Then above the
method, finish things with `@Route("/")`:

[[[ code('') ]]]

## Autoloading

Let's see if things work. Error!

    Attempted to load class "AppBundle" from namespace AppBundle.

The autoloader doesn't know that we have classes living inside the `AppBundle` directory.
In a traditional Symfony project, there's a pre-made `autoload` section in `composer.json`
that takes care of this for us. We need to do this manually.

Add an `autoload` section, with a `psr-4` section below it. Inside, we need just one
entry `AppBundle\\` mapped to `AppBundle/`:

[[[ code('934ca1bc89') ]]]

This says that if a class's namespace starts with `AppBundle`, look for it inside
the `AppBundle` directory. After that, it follows the same namespace rules as always.
You can even rename `AppBundle` to something else like `src` - this is the only line
you'd need to change.

To get the change to take effect, run `composer dump-autoload`:

```bash
composer dump-autoload
```

You don't normally need to run this - it happens after a composer install or update.


## Autoloading Annotations

Try it again. Excellent - the next error!

    The annotation "@Sensio\Bundle\FrameworkExtraBundle\Configuration\Route"
    ... does not exist.

It's doesn't see the `Route` annotation class - it's almost as if we forgot the
`use` statement. If you use annotations in a project, you need one extr autoloading
stuff.

In the `config/` directory, create an `autoload.php` file. Go back to the Standard
Edition code so we can cheat off of it. Find `app/autoload.php` and copy its contents.
Paste that here:

[[[ code('ac89af7d4f') ]]]

I'll delete some comments to make things really small. This includes the normal
autoloader, then adds an extra thing for annotations. Now, in `index.php` - or anywhere
else you need to autoload - require *this* file instead:

[[[ code('986050767e') ]]]

Refresh! Very nice! Let's count the files: 1, 2, 3, 4, 5, 6, 7. We're rocking the
Symfony framework with basically less than 10 files.