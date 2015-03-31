# Requirements #

Please download and install [Python 2.5](http://python.org/) and the [App Engine SDK](http://code.google.com/p/googleappengine/).

Windows and MacOS X users can just follow the standard installation procedure. Linux users can put the SDK in "/usr/local/google\_appengine". If that is impossible you can also put the SDK into any folder that is in the system's PATH environment variable as long as it's called "google\_appengine" (this works with any OS).

If you want to to test a new SDK release you can **temporarily** move it into "common/.google\_appengine".

If you use internationalization (settings: `USE_I18N = True` you also need gettext ([see here](http://docs.djangoproject.com/en/dev/topics/i18n/#gettext-on-windows) if you use Windows). Note that this feature is activated in the sample project, so if you see errors about msgfmt or gettext you should either set `USE_I18N = False` or install gettext.


# Introduction #

We provide a sample project in the [downloads](http://code.google.com/p/app-engine-patch/downloads/list) section that comes with app-engine-patch and a zipped Django package pre-installed. You should change `SECRET_KEY` in your settings.py to some random value and set your appid in app.yaml. For production use you should remove myapp and all its references in the base.html and main.html templates because it contains a view which allows for resetting the admin user.

Most manage.py commands just work (runserver, test, flush, ...), so you should be in a familiar environment. If you want to upload your website to Google you can run "manage.py update". There's also support for removing unused indexes via "manage.py vacuum\_indexes". Note that there is no need to run "manage.py syncdb".

We also provide support for syncdb and [remote access](RemoteAccess.md) to the production datastore.

Before continuing please make sure that you've read the:
  * [Django documentation](http://docs.djangoproject.com/en/dev/)
  * [App Engine documentation](http://code.google.com/appengine/docs/)


# Updating #

If you want to update an existing project please follow these steps:
  * download and unzip the most recent sample project
  * open your project's "common" folder
  * replace everything in your "common" folder with the contents of the sample project's "common" folder
  * read the [release notes](ReleaseNotes.md), so you don't miss any important changes


# Converting #

If you want to convert a plain Django project you have to copy the "common" folder from the sample project into your project and adjust your settings.py (import ragendja.settings\_pre and ragendja.settings\_post) and urls.py (integrate urlsauto). Also, you need to create an app.yaml file. Our [manual installation](ManualInstallation.md) instructions list all the details.

Also read the differences in our Django port below.


# Differences compared to plain Django #

Most of your Django code should just work. Still, you should know about the following details.


## settings.py ##

Please take the sample project's settings structure as a base and modify it to suit your needs. That's easier than writing the settings from scratch. We also define a few useful defaults in [settings\_pre](https://bitbucket.org/wkornewald/appenginepatch-sample/src/tip/common/appenginepatch/ragendja/settings_pre.py) and [settings\_post](https://bitbucket.org/wkornewald/appenginepatch-sample/src/tip/common/appenginepatch/ragendja/settings_post.py). You can override anything from settings\_pre in your own settings. It's also possible to override settings\_post adding the overrides behind the settings\_post import.

Email server settings are only used in the development environment. On the production server you automatically use Google's mail backend. Note that currently Google's SDK doesn't work with gmail accounts and other servers that require TLS. This is a bug in App Engine and we've already filed a bug report.

You can't use the `TransactionMiddleware` due to conceptual differences which make it impossible to implement in App Engine.

The most essential "django.contrib" apps and apps that don't depend on models are supported (auth, sessions, webdesign). The following apps haven't been ported, yet:
  * `django.contrib.comments`
  * `django.contrib.contenttypes` (partially available; generic relations not supported)
  * `django.contrib.databrowse`
  * `django.contrib.gis`

## Models ##

We only support [App Engine models](http://code.google.com/appengine/docs/datastore/) (`from google.appengine.ext import db`). Django models don't work. There are quite a few conceptual differences, especially with transactions, unique properties, and queries, so we can't emulate Django in this area.

Anything that gets filter parameters like Django's `get_object_or_404()` isn't supported, but we provide alternatives in [ragendja.db](RagendjaDB.md) with an API that is similar to that of Google's `Model.all().filter()` call. In contrast, most Django features that just use the datastore are available (e.g., all `ModelForm` features).

Starting with app-engine-patch 1.0 the model `kind()` gets prefixed with the app label of the app which defines that model. For example, if you have `myapp.models.MyModel` the datastore will store entities with the following entity name: `myapp_mymodel`. This is needed to prevent conflicts when multiple apps define models with the same name. Note that Django uses this naming scheme, too, when creating SQL tables. If necessary, you can disable that feature in your settings.py:

```
DJANGO_STYLE_MODEL_KIND = False
```


## Admin interface ##

If you specify an ordering for a model you have to take care of providing all necessary datastore indexes. The user can't override the ordering because this would require a huge amount of indexes (combine orderings with filters and searches and you're in big trouble).

Searches only work on one single property and they're exact, by default. With [gae-search](http://gae-full-text-search.appspot.com/) you can also enable full-text search for a SearchIndexProperty (e.g., named "index") like this:

```
class PersonAdmin(admin.ModelAdmin):
    search_fields = ['@index']
admin.site.register(Person, PersonAdmin)
```

It's impossible to search for key\_name, key\_id, or key. In the worst case you can enter the str(key) URL directly into your browser. Just look at some existing entity and modify the URL.


## User model ##

Since there are no unique properties in App Engine the User model doesn't guarantee uniqueness of the `username`.

Groups are emulated with a ListProperty storing db.Key objects ([KeyListProperty](RagendjaDB.md)), but if you use a ModelForm the user will get a nice selector interface as if it were a ManyToManyField.

## Permissions ##

Permissions aren't real models, but just objects which can be converted to a string via permission.get\_value\_for\_datastore(). Permission objects get dynamically generated based on the installed models. In the User model they're stored in a FakeModelListProperty, but ModelForm will display them with a selector interface as if it were a ManyToManyField.

```
from ragendja.dbutils import FakeModelListProperty
from google.appengine.ext import db
from django.contrib.auth.models import Permission

class MyModel(db.Model):
    # This property is a list of Permission objects
    permissions = FakeModelListProperty(Permission)
```


## Generic views ##

The `object_list`, `create_update`, and `simple` modules are supported. The `date_based` generic views can't be implemented efficiently on App Engine and thus won't be supported.

If a generic view expects an `object_id` parameter you may provide a number to indicate a key\_id or a string to indicate a `key_name` or `str(key)`.

The number of search results shown by `object_list` is limited to 301, so you don't have to mess with datastore timeouts or too large requests. Why exactly 301 results? That's because you probably want to say "more than 300 results" on your search results page. ;) If you want to handle more results than that you have to use a more [scalable search method](http://sites.google.com/site/io/building-scalable-web-applications-with-google-app-engine).

The `post_xxx_redirect` parameters provide special access to `%(key)s`, `%(key_name)s`, and `%(key_id)d`.


## Content types ##

Similar to Permission, content types aren't real models. They get generated dynamically based on the installed models. You can use a FakeModelProperty which is very similar to a ReferenceProperty

```
from ragendja.dbutils import FakeModelProperty
from google.appengine.ext import db
from django.contrib.contenttypes.models import ContentType

class MyModel(db.Model):
    content_type = FakeModelProperty(ContentType, required=True)
```

Generic relations aren't supported, yet.


## Sites ##

The `SITE_ID` must be the `str(key)` of the Site entity. You should also have a look at the [DynamicSiteIDMiddleware](RagendjaOther.md).


# Paths #

The default project handler adds "common/appenginepatch" and "common" to sys.path.
This means that you don't have to specify "common.xxx" in your imports.
For example, you can import something from ragendja like this:
`from ragendja.testutils import ModelTestCase`

Never import `from projectname.appname`, but import `from appname` directly. This also has the advantage that your code becomes more reusable.

# Adding custom patches to your project #

If you want to add a few custom patches you have to write your own main.py handler. You could copy the original main.py and modify the code, but then you'd have to track changes in app-engine-patch and update your main.py manually. There's an easier alternative. Write your own main.py and import everything from the original main.py:

```
from common.appenginepatch.main import *

# Do your own patching here
# ...

if __name__ == '__main__':
    main()
```

# Diving in #

Please read our [documentation](Documentation.md). There are lots of goodies which you don't want to miss.