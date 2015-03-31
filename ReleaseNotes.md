# Release 1.1RC (2009-08-02) #
What's new:
  * Django 1.1 support

Also read the release notes of the previous versions to ensure a flawless upgrade.

# Release 1.0.2.3 and 1.1beta1.1 (2009-07-09) #
**Upgrade notes:** Since the [media generator](MediaGenerator.md) now rewrites `url()` paths you have to change your CSS files: `url(<app>/image.png)` now becomes simply `url(image.png)` if that code is in a CSS file in the same folder or `url(../<app>/image.png)` if it's in a CSS file in some other app (i.e., you can use '../' to get the parent folder).

**Upgrade note 2:** Please make sure you also follow the upgrade notes for 1.0.2 if you currently use an older release.

What's new:
  * [Media generator](MediaGenerator.md) rewrites `url()` paths in CSS files such that they're relative to the CSS source file instead of the destination file (this simplifies integrating 3rd-party modules) - thanks mback2k!
  * Signals also work with batch-puts/deletes (db.put/delete)
  * A 500 error page can be enforced to show a traceback by setting DEBUGKEY in settings.py and adding "?debugkey=`<DEBUGKEY>`" to your URL
  * Admin interface can utilize SearchIndexProperty from [gae-search](http://gae-full-text-search.appspot.com/)
  * Fixed User.get\_profile()
  * Fixed bugs in FormSetField
  * Work around PYTHONPATH conflicts when you have other "google" packages installed
  * Other minor bugfixes

Note: 1.0.2.2 missed a change regarding the `url()` rewrite behavior, so we had to make a quick update.

# Release 1.0.2.1 (2009-06-18) #
This release contains a bugfix for FakeModelProperty which prevented you from assigning groups/permissions to users. If you upgrade from release 1.0 please make sure you've read the 1.0.2 upgrade notes below:

# Release 1.0.2 (2009-06-16) #
**Upgrade notes:** We've added a remoteapi handler to app.yaml (for remote access to the production server). Please integrate that, so features like update, syncdb, and remote shell work correctly.

The [app settings feature](SelfContainedApps#Automatic_app_settings_integration.md) has changed. Form now on, you manipulate the settings module directly. Change your import to: "`from ragendja.settings_post import settings`" and work with the settings module. Also, `add_app_media()` now doesn't need the ugly `globals()` parameter.

Also, if you use the [urlsauto feature](SelfContainedApps#Automatic_app_urls_integration.md) feature you have to rename the `urlpatterns` variable to `rootpatterns`. In simple cases where you map directly to "`/<appname>/`" you can just remove the existing urlsauto.py and rename your urls.py to urlsauto.py.

Finally, don't forget to update the "registration" app if you use that in your project.

What's new:
  * included [jQuery](http://jquery.com/) and [Blueprint CSS](http://www.blueprintcss.org/) in the sample project to demonstrate our [media generator](MediaGenerator.md) - thanks to Tom Brander (dartdog) for providing a base package
  * [remote access](RemoteAccess.md) via remoteapi - thanks a lot Thomas Bohmbach (from Best Buy/Giftag) for your help
  * manage.py syncdb support (also for production server via remote access; syncdb is automatically executed after manage.py update)
  * fixed a bug that could cause strange behavior (e.g., DuplicatePropertyError) if you got an exception on the first request of a new instance
  * fixed a bug that caused sys.path to be reset to its initial state (could appear when an exception is raised on an instance's first request)
  * improved [urlsauto feature](SelfContainedApps#Automatic_app_urls_integration.md)
  * removed `GLOBALTAGS` feature in order to reduce the number of pre-loaded modules (lazy loading is better to prevent `DeadlineExceededError`)
  * added a few minor optimizations in order to slightly reduce instance load times
  * fixed a security issue in encodejs (data wasn't escaped)
  * minor changes

# Release 1.0 (2009-02-24) #
**Very important:** If you upgrade an existing project please set `DJANGO_STYLE_MODEL_KIND = False` in your settings.py. From now on, the model `kind()` gets prefixed with the app label, by default, which prevents naming conflicts when multiple apps define models with the same name. Note that Django uses this naming scheme, too, when creating SQL tables. This feature breaks existing models which are stored without the app label, so you need to disable it when upgrading your project (or you can override the kind() method for existing models).

**Important**: In beta3 we introduced a Django compatibility fix which makes your existing data inaccessible if you use `DJANGO_STYLE_MODEL_KIND` (introduced with 1.0beta and enabled by default). You can manually override your models' db\_table in class Meta (or the models' kind()) to stay backwards-compatible with 1.0beta1/beta2.

**Important:** Please upgrade these files according to the versions in the sample project, so they contain the features required for [self-contained apps](SelfContainedApps.md):
  * app.yaml (default expiration and static file handler)
  * settings.py (import ragendja.settings\_pre, ragendja.settings\_post)
  * urls.py (urlsauto and auth\_patterns)
  * manage.py (media generator and i18n compiler helper)

What's new:
  * ported Django's admin interface
  * [media generator](MediaGenerator.md)
  * [self-contained apps](SelfContainedApps.md)
  * ported django.contrib.sites and django.contrib.flatpages (thank you, Rafał Jońca!)
  * ported and integrated django-registration into sample app (thank you, Jesaja Everling!)
  * support for groups and permissions
  * ported FormSet and Django's native ModelForm (thank you, Norman Rasmussen!)
  * ported django.contrib.redirects
  * added [dynamic SITE\_ID middleware](RagendjaOther.md) for hosting multiple sites on a single Django instance
  * added [KeyListProperty](RagendjaDB.md) for simulating simple many-to-many relations
  * removed our custom httplib and urllib2 emulations (App Engine has its own, now)
  * partially ported django.contrib.contenttypes (generic relations don't work)
  * memcache-optimized sessions backend
  * added [profiler](Profiling.md) features: analyze only specific requests or a certain percentage of all requests
  * added support for Django's Model signals
  * fixed support for most recent boto release (httplib extensions)
  * fixed `get_list_or_404` to be consistent with `get_object_or_404`
  * fixed support for file uploads
  * fixed slug support and added support for key\_name and other redirect keywords in generic views
  * added support for model Meta `verbose_name` and `verbose_name_plural`
  * print app-engine-patch version number when running manage.py
  * refactored and cleaned up code (no more monkey patching for Django)

Thanks to everyone who took the time to report bugs!

# Release 0.9.4 (2009-02-21) #
  * updated to be compatible with SDK 1.1.9

# Release 0.9.3 (2009-01-18) #
  * updated to be compatible with SDK 1.1.8

# Release 0.9.2 (2008-11-19) #
  * updated to Django 1.0.2 ([release notes](http://docs.djangoproject.com/en/dev/releases/1.0.2/))

# Release 0.9.1 (2008-11-18) #
  * fixed translation support when using a zipped Django package
  * fixed SDK detection via PATH for Linux/OSX

**Important:** Please move "common/django-locale/`*`" to "common/django-locale/locale/" and remove the `LOCALE_PATHS` setting for that folder.

# Release 0.9 (2008-11-16) #
  * updated Django to version 1.0.1
  * support for all `django.contrib.auth` forms and views
  * support for [Google Accounts](GoogleAccounts.md)
  * serialization support
  * `ragendja.dbutils.prefetch_references()` function for optimizing datastore access
  * fixed exception logging
  * fixed a few minor issues

Note: Starting with this release we'll only provide a sample project. If you're updating an existing project please also update your Django version to the one shipped with the sample project.

# Release 0.8 (2008-10-12) #
  * automatic integration of [zip packages](ZipPackages.md)
  * support for boto's SQS implementation
  * djangoforms patch, so you can use lazy translations for verbose\_name in model fields
  * httplib workaround for headers that aren't strings (e.g., boto sets them)
  * fixed encodejs escaping in templates
  * you can directly set the value of a KeyReferenceProperty
  * added support for User instances with legacy MD5 hashes that don't have a salt (thank you, Curtis Thompson!)
  * fixed a bug that could rarely cause import errors

**IMPORTANT**: We've renamed ragendja.templatutils to ragendja.templatetags.ragendjatags, so it can be used like any other templatetags library. Please change your imports.

**NOTE**: The sample project now comes with a zipped Django package!

# Release 0.7 (2008-09-04) #
  * Django 1.0 support
  * added a sample project to get you started more easily
  * support for all generic views except for the `date_based` ones which can't be implemented efficiently with the datastore (i.e.: we support `simple.*`, `object_detail.*`, `create_update.*`)
  * `@staff_only` view decorator that renders `no_access.html` if `user.is_staff` is `False`
  * added variable that can be used to check whether we run on the dev\_appserver or the real GAE production servers (`from appenginepatcher import on_production_server`)
  * limit number of results in `object_list` generic view to 300, so you won't have problems with timeouts
  * `httplib` should work with most recent SDK
  * `generate_key_name` utility function that allows for generating a `key_name` from a set of values (building a path-like string to uniquely identify an object by its `key_name`)
  * fixed unicode email support

**NOTE:** The `is_banned` field has been moved to the EmailUser model, so the default User is more compatible with Django.

# Release 0.6 (2008-08-15) #
  * `logging.debug` should work if `settings.DEBUG` is `True`
  * `GLOBALTAGS` work without having to import `ragendja.template` (i.e., generic views should be supported, too)
  * `httplib` also works with `manage.py shell`
  * updated code to be compatible with Django 1.0beta1 (sessions, db backend, signals)
  * PATH is searched for a google\_appengine SDK installation, too
  * a few simplifications

**IMPORTANT:** This release requires Django 1.0beta1. The version jump (0.3.4 => 0.6) isn't due to new features. The previous version number simply didn't reflect the actual state of the project correctly.

# Release 0.3.4 (2008-07-31) #
  * Linux: added shebang to `manage.py`, so you can run it as an executable
  * Windows: the "Program Files" folder and system drive get chosen correctly
  * fixed httplib emulation (e.g., needed by boto)
  * removed custom cache backend
  * added `get_object_or_404()` and `get_list_or_404()` replacements to ragendja.dbutils
  * added `db_add()` function to ragendja.dbutils (only adds an entity if it didn't exist, yet)

**IMPORTANT:** This release is only compatible with Django 1.0alpha and above. You now have to use Django's memcached backend explicitly by adding this to your settings:
```
CACHE_BACKEND = 'memcached://'
```
The GettingStarted article has been updated with more details.

Also, we've updated the [documentation](Documentation.md) a little bit, so please take a look!

# Release 0.3.3 (2008-07-15) #
  * fixed datastore access for `manage.py` commands

# Release 0.3.2 (2008-07-07) #
  * fixed `manage.py test`
  * [integrated profiler](Profiling.md) (can be enabled in settings.py)

# Release 0.3.1 (2008-07-03) #
  * fixed bugs that prevented appenginepatch from working on production server

# Release 0.3 (2008-07-03) #
  * manage.py support
  * compatibility fixes in User model

**IMPORTANT:** Make sure that you use `DATABASE_ENGINE = 'appengine'` and add `'appenginepatcher'` to your `INSTALLED_APPS`.