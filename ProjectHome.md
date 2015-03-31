<br /><br />
# Switch to Django-nonrel #
<table width='2300' border='0'>
<blockquote><tr>
<blockquote><td></blockquote></blockquote>

<table width='2300' border='0'><tr>
<td width='1920'>You should use Django-nonrel from now on because <b>app-engine-patch has been discontinued!!!</b></td>
<blockquote><td><a href='http://www.floqus.com'><font color='white' size='1'>diagramming software ideation</font></a> <font color='white' size='1'>and</font> <a href='http://www.pentotype.com'><font color='white' size='1'>wireframing software prototyping</font></a></td></tr>
</blockquote><blockquote></table>
</td>
</tr>
</table></blockquote>

<br /><br />
Join the [Django-nonrel](http://www.allbuttonspressed.com/projects/django-nonrel) project and follow the [Django-nonrel blog](http://www.allbuttonspressed.com/blog/django) (which is written with Django-nonrel) if you want to use Django's ORM on App Engine and other NoSQL / non-relational databases. Django-nonrel will allow to run your code on App Engine, MongoDB, Cassandra, etc., and SQL without any modifications and at the same time make you many times more productive by automating NoSQL practices such as denormalization for you! That's the plan and we've come pretty far (there's even full-text search with [nonrel-search](http://www.allbuttonspressed.com/projects/nonrel-search)) and we support 99% of App Engine's features (in theory all features can be supported), but we need help with the really advanced stuff and more backends, so we can get officially integrated into Django.
<br /><br />
<br /><br />
<br /><br />
<br /><br />
<br /><br />
<br /><br />
# app-engine-patch #
With app-engine-patch a major part of Django works on App Engine without any modifications. The most important change is that you have to use Google's `Model` class because the development model is too different from Django (at least with Django's current API).

We also provide lots of useful extra features (see below).

**Did you know?** [Giftag](http://www.giftag.com/) is powered by app-engine-patch! See other [projects using app-engine-patch](ProjectsUsingAppEnginePatch.md).

# Full-text search #

Add full-text search to your site with just a few lines of code: [gae-search](http://gae-full-text-search.appspot.com/)

# Latest release: 1.1RC on Aug 02, 2009 #

New features: Django 1.1 support. See [release notes](ReleaseNotes.md)

[Download now!](http://app-engine-patch.googlecode.com/files/app-engine-patch-1.1RC.zip) --- [See it in action](http://aep-sample.appspot.com/) --- [Get started](GettingStarted.md) --- [Release news feed](http://code.google.com/feeds/p/app-engine-patch/downloads/basic)

**Reminder:** Read the [release notes](ReleaseNotes.md) to make sure you don't miss anything before you upgrade.

Please support us by placing this logo/link on your site:
<a href='http://code.google.com/p/app-engine-patch/'><img src='http://app-engine-patch.googlecode.com/files/powered-by-app-engine-patch.png' alt='powered by app-engine-patch' /></a>

# Quickstart #

Extract the sample project. Run the dev\_appserver with `manage.py runserver`. Upload your website with `manage.py update` (don't forget to set your appid in app.yaml). [Read more...](GettingStarted.md)

# Feature highlights #
## app-engine-patch ##

  * Django's admin interface
  * Django's authentication framework and optional integration with [Google Accounts](GoogleAccounts.md)
  * Django's sites, flatpages, redirects, and some parts of contenttypes
  * Django's session and cache backends (db sessions optimized with memcache)
  * Support for Django's mail functions
  * More complete `ModelForm` (than `djangoforms`) via `django.forms` package
  * Support for generic views (`list_detail`, `create_update`, and `simple`)
  * Model serialization/deserialization
  * and much more; [get started](GettingStarted.md)

## ragendja (snippet library) ##

  * Easy to use [zip packages](ZipPackages.md)
  * [Integrated profiler](Profiling.md)
  * [Media generator](MediaGenerator.md)
  * Improved auth framework: You can provide your own `User` class (simplifies your code compared to using `get_profile()`)
  * `LoginRequiredMiddleware` that saves you from adding `@login_required` to most of your views
  * Datastore utils like an `@transaction` decorator and `get_object_or_404()`
  * Shortcuts like `JSONResponse` and a `render_to_response()` based on `RequestContext`
  * Global templatetags (`GLOBALTAGS` in settings.py)
  * An improved template loader that gets rid of file name prefixes (`ragendja.template.app_prefixed_loader`)
  * `ModelTestCase` that allows for verifying the DB contents
  * Special features for writing [self-contained, reusable apps](SelfContainedApps.md)
  * [and much more](http://code.google.com/p/app-engine-patch/wiki/Documentation)

# Comparison to google-app-engine-django (appengine/django-helper) #

Conceptual difference: We don't introduce yet another Model class. Just use Google's Model. In a future release we might support Django's own Model.

Missing features: None! Doesn't that make the decision easy? :)

Our Django port is more complete (e.g., we have the admin interface, sites, flatpages, etc.) and we have lots of extra features ([media generator](MediaGenerator.md), everything in [ragendja](Documentation.md), better ModelForms, ...) and we are constantly improving app-engine-patch to make Django development easier. We also work on features and conventions that allow for [fully reusable plug-n-play-style apps](SelfContainedApps.md), so please just [download](http://code.google.com/p/app-engine-patch/downloads/list) our sample project and [get started](GettingStarted.md).

# Contribute #

Of course, we're always happy about [contributions](Contributing.md), no matter how small. Please release at least a few useful parts of your code as open-source or help us port more parts of Django or other Django apps, so we can make Django development easier on App Engine.