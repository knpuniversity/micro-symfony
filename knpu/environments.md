# Environments

We're kind of abusing the debug flag: it's mostly used to tell Symfony if it should
hide/show errors. If you want control how your app behaves while developing versus
on production, that's normally done with environments: the `prod` environment will
turn on caching and turn off debugging tools. The `dev` environment will do the
opposite.

When `debug` is false, the web debug toolbar is basically off, but the `WebProfilerBundle`
is still being loaded. That's a bit wasteful: why not only instantiate this in the
`dev` environment?

Add `if $this->getEnvironment() == 'dev'`: let's only load the bundle in that scenario.
Change `.env` back to ENV `dev` and DEBUG 1. Refresh now: no issues.

Now change the environment to `prod` and refresh. Ah, error:

    There is no extension able to load the configuration for web_profiler.

This makes sense: now that the `WebProfilerBundle` is *not* being loaded in the
`prod` environment, Symfony doesn't know how to handle the `web_profiler` section
in `config.yml`. The full stack Symfony framework solves this by having environment-specific
`config_dev.yml` and `config_prod.yml` files. But I've gotta keep things small. So,
I'll show you a trick.

Find `registerContainerConfiguration()` and add a new `$isDevEnv` that uses the
same `getEnvironment()` method from before. Next, use `$loader->load()` to load more
configuration. But instead of passing it a file, pass it a Closure that accepts a
`ContainerBuilder` argument. Add the `use` statement on top. Also add a `use` on
the Closure so we have acccess to `$isDevEnv`:

[[[ code() ]]]

Now when Symfony loads, it'll load everything from `config.yml`, but it'll also call
this function, and we can control whatever extra configuration we want. In `config.yml`,
we can't have the `web_profiler` stuff because this file is *always* loaded. Remove
that.

But inside the Closure, we can say: if we're in the `dev` environment, then call
`$container->loadFromExtension()` to pass in the configuration we want. Use `web_profiler`
as the first agument and an array as the second with `toolbar => true`:

[[[ code('') ]]]

That should take care of the first error, so refresh. Closer - that error is gone,
but now there's an error loading the `wdt.xml` routing file. Again, this makes sense:
we're still loading some routes from `@WebProfilerBundle`, which simply doesn't exist
in the `prod` environment:

[[[ code() ]]]

To fix this, create a `routing_dev.yml` file, move the two WebProfilerBundle imports
there, and at the bottom, import the main `routing.yml` file.

Now, do you remember where we told Symfony to load `routing.yml`? It's in `config.yml`:

[[[ code('') ]]]

In *all* cases, we're loading `routing.yml`. But now we need to do something conditional:
in `dev`, we want to load `routing_dev.yml`. Otherwise, we want to load `routing.yml`.
And again, we can do this in `AppKernel`: this is the spot you'll go to if you need
to configure something environment-specific.

If we're in the `dev` environment, call `$container->loadFromextension()`:

[[[ code() ]]]

This time, we need to tweak the `framework` configuration to override the
`router.resource` value. In the array, add a `router` key, set it to an array, then
add `resource` and set it to `routing_dev.yml`:

[[[ code() ]]]

In every environment, we load `config.yml`. But in the `dev` environment, we *override*
the router resource config.

Remember, we're in the `prod` environment. Refresh. No errors, no web debug toolbar.
Change the environment back to `dev` and refresh. Whoops: it doesn't like my `webprofiler`
configuration because that's a typo! Make sure you have `web_profiler`. Now it's
happy *and* we have the toolbar.

The power of the environment is that we can have two different sets of configuration
right on one machine, and toggling between them is effortless. But to minimize files,
we'll keep all the environmental differences inside this Closure.

## Parameters in dotenv

There's one more trick to configuration. In `config.yml` we have a `secret` key.
This should be big, long, randon and - of course - secret. It should *not* be committed
to the repository. Normally, we'd set this to a parameter - like %secret%. Then, we'd
add this to Symfony's `parameters.yml` file and be done. But no more! We can do this
in `.env`.

If I add `SECRET = CHANGEME`, that'll create an environment variable called `SECRET`,
but it will not automatically create a Symfony parameter called `secret`. If I refresh,
we see the error:

    You have requested a non-existent parameter "secret".

Normally, parameters are created by adding a `parameters` key in any configuration
file and listing them below. But we *don't* want to put this stuff here.

The secret is that if you prefix any environment variable with `SYMFONY__`, Symfony
will take what's after that and automatically turn that into a parameter. Refresh.
Now it works perfectly. Goodbye `parameters.yml`, hello `.env` with `SYMFONY__` used
as the prefix.

And don't forget to put this line in the `.env.example` file so new developers have
a chance to get things setup.
