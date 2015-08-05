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

So, step 1 is to install it. At your terminal, run `composer require symfony/monolog-bundle`:

```bash
composer require symfony/monolog-bundle
```

While we're waiting, Google for "symfony monologbundle". The first links is to its
[GitHub page](https://github.com/symfony/MonologBundle), which basically just points
you to read our [official documentation](http://symfony.com/doc/current/cookbook/logging/monolog.html)
on Symfony.

Normally, Symfony users already have this bundle installed in their project. So we
need to do 3 extra steps. First, we needed to grab it with composer. Done! Second,
we need to enable it in `AppKernel`: new `MonologBundle`:

[[[ code('') ]]]

And third - if required - we need to configure the bundle. For that, go back one
more time to the Standard Edition and find the [app/config/config_prod.yml](https://github.com/symfony/symfony-standard/blob/2.8/app/config/config_prod.yml)
file.

See how it feels like a normal third-party bundle?


