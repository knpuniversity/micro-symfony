# Logging and Adding other Tools

Our micro-Symfony is done: go forth and use this as the starting point for new projects.
It's fully-functional, it *looks* a lot like normal Symfony, but it's just a lot
smaller. Mission accomplished.

As you build your project, you may start missing some features that you normally
get out of the box with Symfony, like logging - we don't have *any* logging capabilities
right now.

Symfony logging usually comes from `MonologBundle`: we can look for it in our project,
but nope - we don't have MonologBundle yet. That's no problem: we just need to treat
this normally-core bundle like any other third-party bundle.

## Enabling and Configuring MonologBundle

So, step 1 is to install it. At your terminal, run `composer require symfony/monolog-bundle`:

```bash
composer require symfony/monolog-bundle
```

While we're waiting, Google for "symfony monologbundle". The first link is to its
[GitHub page](https://github.com/symfony/MonologBundle), which basically just points
you to read the [official documentation](http://symfony.com/doc/current/cookbook/logging/monolog.html)
on Symfony.

Normally, Symfony users already have this bundle installed in their project. So we
need to do 3 extra steps. First, we needed to grab it with composer. Done! Second,
we need to enable it in `AppKernel`: new `MonologBundle`:

[[[ code('ca1a2cd27e') ]]]

And third - if required - we need to configure the bundle. For that, go back one
more time to the Standard Edition and find the [app/config/config_prod.yml](https://github.com/symfony/symfony-standard/blob/2.8/app/config/config_prod.yml)
file. Copy most of its `monolog` configuration, it's good stuff! Open up our 
`config.yml` and paste it in. Change `action_level` to debug to log everything.

[[[ code('42eb42c0f1') ]]]

Refresh! No errors. *And* we suddenly have a `logs` directory with a `dev.log` file
in it. Run `tail -f` on that to watch it:

```bash
tail -f logs/dev.log
```

Clear the screen and refresh. Great - it's logging *a lot* of things.

## Controlling action_level

We'll probably *not* want to log *everything* on production like this. So, we need
a way to control the `action_level`. Let's use a parameter: set it to `%log_action_level%`:

[[[ code('1b85844efc') ]]]

And of course if we try it now, we see "non-existent parameter log_action_level".
How can we add it? You already know: go to `.env` and add `SYMFONY__LOG_ACTION_LEVEL`:
Symfony will lowercase that before making it a parameter. Set it to `debug`:

[[[ code('d98c20b322') ]]]

And immediately copy this line into `.env.example`:

[[[ code('634a088448') ]]]

Clear the logs again and refresh. They're still *very* verbose. Change the
level to `error` in `.env`.

Clear the logs and refresh. Hey, *nothing* in the logs: there weren't any errors.
Try a 404 page. Perfect, *all* the logs from the request show up as expected. That's
the job of the `fingers_crossed` handler: don't show me any logs, unless there's
at least one entry that's an `error` level or higher. Then I want everything.

## Enabling dump()

Ok, there's one more thing I love that I'm missing. In the controller, add a 
`dump($this->container->get('twig'))`:

[[[ code('1ffc0db874') ]]]

That's the new awesome `dump()` function
from Symfony 2.6. Right now, it gives us an UndefinedFunctionException:

    Attempted to call function `dump()`.

Ok, this works in Symfony normally, where does it come from? It comes from a DebugBundle.
Head to `AppKernel` and enable it in the `dev` environment: `new DebugBundle()`:

[[[ code('5469c85fb4') ]]]

Try it again. The page loads, and in the web debug toolbar, we see the dumped object.

This *is* Symfony, but it's smaller: you need to enable things when you want them.
The minimum setup includes 4 files in `config`, 1 in `web`, and 3 in the root. Then,
you start building like normal.

Now, take your big idea, spin it up quickly in micro-symfony, and build something
amazing.

Seeya next time!
