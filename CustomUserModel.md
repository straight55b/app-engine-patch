# Defining custom user models #

The `ragendja.auth.models` module now provides base models for customizing your User class:
  * `UserTraits` has only a few basic user fields (see the [source](https://bitbucket.org/wkornewald/django-app-engine/src/tip/contrib/auth/models.py) for more details), but no `username`.
  * `EmailUser` also adds an `email` field, `email_user()`, and an `is_banned` field, but no `username`.
  * `User` is the same as Django's `User` class; it includes a `username`.
The password field is not required because with the new authentication methods
like OpenID and InfoCards you might not need it.

Instead of using `AUTH_PROFILE_MODULE` you can derive your profile from one of the user classes in `ragendja.auth.models`. Then, specify your profile module via `AUTH_USER_MODULE` in `settings.py`. This should only be the module name (e.g., `'myapp.models'`) and the class MUST be named `User`. The result is that whenever you import `User` from `django.contrib.auth.models` you automatically get your custom user class and thus don't need to use the ugly get\_profile() function to switch around between two models. Note that you can't import `django.contrib.auth` from the module that defines your `User` class. If you don't specify a user class then Django's `User` is used by default.

**Warning:** If you use this feature you should not import any other models in the `AUTH_USR_MODULE` because that could cause circular imports. If you still have to import other models make sure they don't try to import the `User` model!

**Example:**
```
# myuser/models.py:
from google.appengine.ext import db
from ragendja.auth.models import EmailUser

class User(EmailUser):
    first_name = db.StringProperty()
    last_name = db.StringProperty()

# settings.py:
AUTH_USER_MODULE = 'myuser.models'
```

With the above code `from django.contrib.auth.models import User` becomes the same as `from myuser.models import User`. In order to maximize code reuse you should always import the Django model in your apps (`from django.contrib.auth.models import User`), so your code is independent of the `User` class of your particular project. In fact, if you don't follow this standard you'll get exceptions.

# Admin interface #

You can also override the UserAdmin class for the admin interface by specifying an `AUTH_ADMIN_MODULE` to your settings.py. Just redefine UserAdmin in that module, but don't register it because that's done in the original `django.contrib.auth.admin` module. You can import the original UserAdmin and derive from it if necessary.

# Google Accounts #

This feature is the foundation of our [Google and hybrid accounts](GoogleAccounts.md) integration.