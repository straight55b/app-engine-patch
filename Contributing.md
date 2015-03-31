# Community building #

Spread the word. Write about app-engine-patch on your blog. Feel free to post a link to your article on our [mailing list](http://groups.google.com/group/app-engine-patch).

We could also need more articles in the [App Engine articles section](http://code.google.com/appengine/articles/) (e.g., about how to use jQuery with the media generator, how to write self-contained apps, how to optimize your DB code, etc.).

Actively participate on our [mailing list](http://groups.google.com/group/app-engine-patch). Answer questions and make suggestions.

Take part in hack-a-thons and tell others about app-engine-patch.

Give presentations at conferences.

# Coding #

First please read [how to get the source](GettingTheSource.md).

Subscribe to our [mailing list](http://groups.google.com/group/app-engine-patch).

Take a look at the list of [open issues](http://code.google.com/p/app-engine-patch/issues/list) or suggest a great new feature yourself! The most useful features would be support for Django's admin interface and the comments app.

## Extending app-engine-patch ##

Basically, app-engine-patch is a collection of monkey-patches and a few extra goodies packaged in ragendja. Normally, we manipulate Django's modules, classes, and functions to be compatible with App Engine in our [django-app-engine repository](http://www.bitbucket.org/wkornewald/django-app-engine/).

A few patches can't be integrated directly into Django and are part of the [app-engine-patch sample repository](https://bitbucket.org/wkornewald/appenginepatch-sample/). As a first step to understand the source code you should first take a look at these files (in "common/appenginepatch"):

  * manage.py
  * aecmd.py
  * appenginepatcher/patch.py
  * main.py


# Sending patches #

In most cases the [rebase extension](http://www.selenic.com/mercurial/wiki/index.cgi/RebaseProject) should be flexible enough to maintain your patches. You can alternatively use the [patch queues extension](http://www.selenic.com/mercurial/wiki/index.cgi/MqExtension) and send a normal diff, but that's more complicated and should be used if you need powerful patch management.

Just send the patch to our [mailing list](http://groups.google.com/group/app-engine-patch) or create a new [issue](http://code.google.com/p/app-engine-patch/issues/list) and attach your patch.