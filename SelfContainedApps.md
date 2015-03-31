The goal of self-contained apps is that you can basically plug a few apps together, write a few lines to connect everything, and get your website finished as quickly as possible.

Unfortunately, Django lacks conventions required for achieving this goal. Many open-source Django apps come without any templates because there is no standard for template blocks. Apps that require JS/CSS don't have any conventions, either. App installation often requires multiple changes in your settings (middlewares, context processors, etc.).

With app-engine-patch we would like to change this and make reusing and installing Django apps as simple as adding them to `INSTALLED_APPS`. Here are our conventions:

# Basic principles #

When you need a model's kind (e.g., in order to create a `Key` object) always use `MyModel.kind()` instead of `MyModel.__name__` or a string like `"MyModel"`.

Never use your project package name in imports. Don't write `from project.app.xxx import yyy`. Instead, write `from app.xxx import yyy`.

Don't hard-code URLs. Always use named urls and `{% url %}` and `reverse()`.

Always pass a `RequestContext` to `render_to_response` (or use [ragendja.template.render\_to\_response](RagendjaTemplate.md) which does it automatically for you).

Never import something in your app's init.py (lazy imports are OK).

There is also a [video introduction](http://www.youtube.com/watch?v=A-S0tqpPga4&feature=channel_page) by an experienced Django developer. Note that while he says that providing default app templates didn't work well the real problem is Django's lack of conventions. That's exactly what we want to solve.

# Template conventions #

Some conventions for templates:

  * use XHTML (you can expect XHTML 1.0 Transitional)
  * always place your app templates directly into the app's "templates" folder (e.g., "myapp/templates/somepage.html")
  * always name the base template "base.html" and extend from that template
  * the page title goes into the "title" block
  * the content goes into the "content" block
  * your css files go into the "css" block
  * your js files go into the "js" block (which always comes after the css block)
  * any extra information that must be in the `<head>` goes into the "extra-head" block

# Automatic app settings integration #

**Important:** Only use this feature with apps that don't place any imports in their init.py because you otherwise get in trouble with recursive imports! If you have no control over the source you can tell ragendja to not load that app's settings (see below).

The sample project is pre-configured to load a settings.py module from each installed app. From there, you can manipulate the main settings module (which means you can override global settings):

```
# myapp/settings.py:

# Import the main settings module
from ragendja.settings_post import settings

# Append a new middleware class
settings.MIDDLEWARE_CLASSES += (
    'myapp.middleware.MyAppMiddleware',
)
```

As you can see, this reduces the amount of work users need to do in order to install an app. If, for example, some middleware class needs to **always** be installed you can just add it automatically and take the burden from the user.

You can disable this on a per-app basis in your settings:
```
IGNORE_APP_SETTINGS = (
    'registration',
    'myapp',
)
```

# Media generator #

If you combine this feature with app settings integration you can automatically add all required JS/CSS files to your project. For this we provide the utility function `add_app_media` which inserts your media files **before** the media files defined in the global settings:

```
# myapp/settings.py:

from ragendja.settings_post import settings

settings.add_app_media('combined-%(LANGUAGE_CODE)s.js',
    'myapp/bla.js',
    'myapp/blub.js',
    # ...
)
settings.add_app_media('combined-%(LANGUAGE_DIR)s.css',
    'myapp/style.css',
    # ...
)
```

As a convention, app-engine-patch expects that you name your main media files "combined-%(LANGUAGE\_CODE)s.js" and "combined-%(LANGUAGE\_DIR)s.css". Use that if you want your app to be reusable.

See the [media generator documentation](MediaGenerator.md) for more information.

# Automatic app urls integration #

The `ragendja.urlsauto` module allows for automatically integrating the installed apps' urls with a default base URL (for apps that support this feature). To install, just change your main urls.py like this:

```
# urls.py:
# ...
# Import default app urlpatterns
from ragendja.urlsauto import urlpatterns
# Append imported app url patterns to our patterns:
urlpatterns = patterns(...) + urlpatterns
```

Then, you can simply define an "urlsauto.py" module with an `urlpatterns` and/or `rootpatterns` variable in your app. For example, we've extended the registration app this way:

```
# registration/urlsauto.py:

from django.conf.urls.defaults import *

# Integrate registration app under /account/...
rootpatterns = patterns('',
    (r'^account/', include('registration.urls')),
)
```

This will append `rootpatterns` to your global urls.py. Here, we mapped the "registration" app to "/account/". There is a shortcut for the common case of mapping to "`/<appname>/`" (in this case: "/registration/"). Instead of storing your patterns in `rootpatterns` you just store then in `urlpatterns`. In other words, simply rename your app's urls.py to urlsauto.py and all urls will be prepended with "`/<appname>/`".

## Disabling urlsauto ##

You can disable this feature on a per-app basis in your settings:
```
IGNORE_APP_URLSAUTO = (
    'registration',
    'myapp',
)
```