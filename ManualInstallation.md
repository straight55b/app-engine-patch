You can of course install app-engine-patch manually, but it's more difficult than starting with the sample project. Even if you want to use the most recent source directly you can just download the sample project and replace the "common" folder with a clone of the app-engine-patch repository. If you're still not convinced then let's get started:

First, grab our [source code](GettingTheSource.md). Use the following project structure:

```
project root
  __init__.py
  app.yaml
  settings.py  (based on sample project with imports for settings_pre and settings_post)
  manage.py    (copy from "appenginepatch")
  common
    __init__.py
    appenginepatch     (copy the whole main folder)
    django_aep_export  (based on sample project; contains Django's media files and templates which can't live in a zip file)
    zip-packages
        django.zip     (our patched Django version)
```

Don't forget to copy manage.py from the "appenginepatch" folder into your project root. You can only execute it from your project's root folder.

Your app.yaml could look like this:

```
application: sample
version: 1
runtime: python
api_version: 1

default_expiration: '3650d'

handlers:
- url: /media
  static_dir: _generated_media

- url: /.*
  script: common/appenginepatch/main.py
```

This will use the default main.py handler which should work for most projects and it'll automatically use the `_generated_media` folder of our [media generator](MediaGenerator.md).


# Zipping a Django package #

First, read the [zip packages documentation](ZipPackages.md).

Since you can't load translations from a zip file you have to move the contents of "django/conf/locale" to your project's "common/django\_aep\_export/django-locale/locale". This folder automatically gets integrated when using a zipped Django package. Create a "django.zip" file and move it into the "zip-packages" folder.

As a last step, move all templates and media files from the zip file (under "django/contrib") into the respective folder in "common/django\_aep\_export". See the sample project for an example. It's easier to show than to explain. :)


# settings.py #

All you need to do is add `'appenginepatcher'` to your `INSTALLED_APPS`, set `ROOT_URLCONF = 'urls'`, set `DATABASE_ENGINE = 'appengine'`, and remove all other database settings (host, username, password, etc.).

Also, use the same structure as in the sample project. There, we import ragendja.settings\_pre at the top and ragendja.settings\_post at the bottom. Those files define better default values for App Engine (e.g., for file upload handling) and they add support for some [media generator](MediaGenerator.md) and our [self-contained apps](SelfContainedApps.md) features.

The cache backend's default timeout is "infinite" because that's the only way to specify it. So when you don't specify a timeout manually you get infinite timeout. Normally you should use a timeout that fits the task at hand.
```
CACHE_BACKEND = 'memcached://?timeout=0'
```