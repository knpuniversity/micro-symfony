# Web Debug Toolbar and Profiler

Right now, there's no floating web debug toolbar on our pages. That's one of the
best features of Symfony, and so I want it in my micro-edition too.

Instead of getting a lot of features automatically, we need to enable things one-by-one
when and if we need them. The same is true for the web debug toolbar: it comes from
the `WebProfilerBundle`. This lives in our project, but it's not enabled yet. Add
it in `AppKernel` down below the others:

[[[ code('') ]]]

Next, head into `config.yml` - we need a little bit of configuration. Add a `web_profiler`
key with a `toolbar` option. Set this to `%kernel.debug%`:

[[[ code('') ]]]

That's a special parameter that's equal to the `debug` argument we pass to `AppKernel` 
in `index.php`:

[[[ code('') ]]]

Mostly, this flag is used to hide or show errors. But for now, we'll also use it
to decide if the web debug toolbar should show up or not.

Also, under `framework`, add a `profiler` key with an `enabled` option that we'll
also set to `%kernel.debug%`:

[[[ code('') ]]]

We have debug set to true, so try it! No toolbar yet - just this nice error:

    Unable to generate a URL for the named route "_wdt" as such
    route does not exist.

This is the error you usually get if you're generating a URL to a route, but you're
using a bad route name. The web debug toolbar and profiler are fueled by real Symfony
routes, and those routes need to be included for this all to work. On the Standard
Edition, I already have the `app/config/routing_dev.yml` file open, because we need
to copy the top two entries. Find `config/routing.yml` and paste those there:

[[[ code('') ]]]

Try one more time. There's the toolbar, with all of the goods we love.
