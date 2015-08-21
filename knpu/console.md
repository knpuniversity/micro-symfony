# Where's my app/console

We have environments and everything is going great, but we don't have an `app/console`
script. So we can't use any of the handy commands like `debug:router` or `debug:container`.
*And*, if you surf to a 404 page, it's still missing its CSS. That's because we need
to run `app/console assets:install` so that it can symlink or copy a few core CSS
files that the exception page uses. That's a bit difficult right now, since there
is no console script in our project.

Let's go steal it from the standard edition. On GitHub, head into the app directory,
open `console` and copy its contents. Now, put our new `console` file right at the
root of the project and make sure PhpStorm formats it as a PHP file. Paste the contents:

[[[ code('57604d991d') ]]]

Let's go to work: uncomment the `umask`. For the first `require_once`, we know that
this should be `config/autoload.php`. And `AppKernel` lives right in this directory,
so that's fine:

[[[ code('218e92403f') ]]]

The biggest thing we need to change is the `$env` and `$debug` variables. Let's grab
that code from `index.php`. PhpStorm is being weird and hiding my `web/` directory...
now it's back. Copy the 4 dotenv lines from `index.php`. Now in `console`, delete
the `$env` and `$debug` lines and paste our stuff:

[[[ code('42dd0c174f') ]]]

Instead of passing a `--env=prod` flag to change the environment, this reads
the `.env` file like normal. Let's try it!

If you're on UNIX, make this executable with a `chmod +x`. Then run `./console` or
in Windows `php console`:

```bash
chmod +x console
./console
```

That blows up with "Environment file .env not found". I forgot to update my `DotEnv`
path since this file lives in the root. Make sure you pass it just `__DIR__`:

[[[ code('b03295d65b') ]]]

Try it now:

```bash
./console
```

Our mini-app has a fully-fledged console. Instead of `./app/console`, the command
is just `./console`, but everything else is the same. Use `debug:router` to get all
the routes:

```bash
./console debug:router
```

And `debug:container` to see the services:

```bash
./console debug:container
```

And the one we want is `./console assets:install` with a `--symlink` if your system
supports that.

```bash
./console assets:install --symlink
```

Refresh the 404 page: there's the beautiful exception page we expect.
