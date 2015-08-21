# dotenv: Environmental Variables

The `$debug` and `$environment` variables are hardcoded in `index.php`. So if we
deploy this, we'll either have a web debug toolbar on production, or we'll need to
manually modify this file to set `$debug` to false. Both situations stink.

## PHP dotenv Fanciness

To help, we'll install a new library: `composer require vlucas/phpdotenv`:

```bash
composer require vlucas/phpdotenv
```

While we're waiting, Google for that library and find its
[documentation](https://github.com/vlucas/phpdotenv).

This is a popular library these days, and I really like it too. It allows you to
have a `.env` file at the root of your project. It'll look something like this:

```
S3_BUCKET="dotenv"
SECRET_KEY="souper_seekret_key"
```

This library reads these values and turns `S3_BUCKET` and `SECRET_KEY` into
environment variables. Then, in our app, whenever we need some configuration -
like whether we're in debug mode, the database password or the S3 bucket -
we'll read from the environment variables. When you eventually deploy, you can set
those variables in two different ways. First, you can have a `.env` file. Or
second, you can set the variables via something like your web server configuration.
Some platforms - like Heroku also have a way to set environment variables. The
important point is that your app isn't bound to a configuration file: it's just
reading environment variables, which is a pretty standard way of setting config.

## Our .env File

The first things we want to set are the `$env` and `$debug` flags. Create a `.env`
file and say `SYMFONY_ENV=dev` and `SYMFONY_DEBUG=1`:

[[[ code('8d5e2baf75') ]]]

Remove the old variables in `index.php`. Replace it with `$dotenv = new DotEnv\DotEnv()`.
The argument is the directory where the `.env` file lives - it's actually up one
directory from here. Then, call `$dotenv->load()`:

[[[ code('a6369d30f7') ]]]

At this point, those two flags have been set as environment variables. That means
we can say `$env = $_SERVER['SYMFONY_ENV'];` and `$debug = $_SERVER['SYMFONY_DEBUG'];`:

[[[ code('ed50ab99f6') ]]]

Go back and refresh the new setup - we should still see the toolbar. Yep, there it
is. But now go back to `.env` and set `SYMFONY_DEBUG` to 0. Because of `config.yml`,
this should turn the toolbar off. Change the environment to `prod` too - that's not
being used anywhere yet, but it may avoid a temporary cache error:

[[[ code('a2389908a0') ]]]

Try it out: no web debug toolbar.

Add `.env` to the `.gitignore` file - this shouldn't be committed:

[[[ code('46bf4a2908') ]]]

But copy `.env` to `.env.example` - we will commit this so that new developers have
something they can use as a guide.
