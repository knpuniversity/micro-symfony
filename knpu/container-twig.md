# Container, Twig: All Smooth

When you want to render a template, you'll usually extend `Controller` and it'll give
you a bunch of shortcuts, including `render()`. That's a great idea. But I'll extend
`ContainerAware` instead:

[[[ code('7c6440d98a') ]]]

That gives access to the service container, but not the shortcut methods. So I can't
say `$this->render()`, but I *can* say `$html = $this->container->get('twig')` and
then call `render()` on it. Render `mighty_mouse/rescue.html.twig` and pass it a
`quote` variable. But that's just HTML - so wrap it in a `Response` and return:

[[[ code('b79254a870') ]]]

Now, where do templates live? Usually, it's `app/Resources/views`. But we've moved
everything up and *out* of `app/`. So our path is just `Resources/views`: add
those directories and a `mighty_mouse` folder inside. Create `rescue.html.twig` and
extend the base template. We don't have a base template yet, but we will soon. In
`{% block body %}` print out the quote:

[[[ code('49e3f835e7') ]]]

Now add `base.html.twig` in `Resources/views`. Let's steal again from the
Standard Edition. Find `app/Resources/views/base.html.twig`, copy it, and paste it
in ours:

[[[ code('a50873b724') ]]]

Ok, give it a try. Unable to find template "mighty_mouse/rescue.html.twig". Hmm
I bet I have a typo. Yep, my directory was called `might_mouse`. I went a bit too micro
on the name there. Fix that and try again. It's alive!

***TIP
After creating the `views/` directory for the first time, you'll need to clear
your Symfony cache, or the template won't be found. That's probably a small Symfony
bug, that hopefully we can fix!
***

Now, let's add the web debug toolbar.
