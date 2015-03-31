# Using Google Accounts #

It's possible to use Google Accounts instead of Django's auth mechanism or alternatively use a hybrid system that supports both authentication methods.

By default, Google Accounts marked as admins get superuser and staff status when logging in for the first time. You can turn this off in your settings:
```
AUTH_ADMIN_USER_AS_SUPERUSER = False
```

## Google Accounts only ##

You just need a few changes in your settings.py:

```
# Replace Django's AuthenticationMiddleware with GoogleAuthenticationMiddleware.
MIDDLEWARE_CLASSES = (
    # Note: You don't need the SessionMiddleware for Google Accounts
    ...
    'ragendja.auth.middleware.GoogleAuthenticationMiddleware',
    ...
)

# Change the User model and admin classes
AUTH_USER_MODULE = 'ragendja.auth.google_models'
AUTH_ADMIN_MODULE = 'ragendja.auth.google_admin'
```

Note that the default Google `User` model does not have `first_name` and `last_name` properties, but it adds a `user` property of type `db.UserProperty`. Also, the `email` and `username` properties can't be changed or queried with a `filter()` call because they are dynamically retrieved from the `user` property. You can [define your own](CustomUserModel.md) `User` model if you want to change anything.

Use the [LoginRequredMiddleware](RagendjaAuth.md) to ensure that the admin interface uses Google logins, too.

## Hybrid authentication ##

With hybrid authentication you can use both Google and Django accounts. Just add this to your settings.py:
```
# Replace Django's AuthenticationMiddleware with HybridAuthenticationMiddleware.
MIDDLEWARE_CLASSES = (
    ...
    'ragendja.auth.middleware.HybridAuthenticationMiddleware',
    ...
)

# Change the User model class
AUTH_USER_MODULE = 'ragendja.auth.hybrid_models'
```

Here, the `User` model has all properties that are defined in Django in addition to a `user` property (`db.UserProperty`) which can be set to `None`.

# Automatic auth method handling #

If you don't want to distinguish between authentication methods, manually, you can integrate ragendja's login and logout views which automatically adapt to your authentication method. This also works with all auth decorators and `redirect_to_login`.

```
# urls.py:

from django.conf.urls.defaults import *
from ragendja.urlsauto import urlpatterns
from ragendja.auth.urls import urlpatterns as auth_patterns

urlpatterns = auth_patterns + patterns('',
    ...
) + urlpatterns
```

You can integrate those views in your template with:

```
<div class="login">
  {% if user.is_authenticated %}
    Welcome, {{ user.email }}
    <a href="{% url django.contrib.auth.views.logout %}">Logout</a>
  {% else %}
    <a href="{% url django.contrib.auth.views.login %}?redirect_to={{ request.get_full_path|urlencode }}">Login</a>
  {% endif %}
</div>
```

Above, we could override the `LOGIN_REDIRECT_URL` with the `redirect_to` parameter. You can also specify a `LOGOUT_REDIRECT_URL` in your settings if you don't want to show a "You are logged out." template.

# Manual auth method handling #

## Template tags and context processors ##

Add `'ragendja'` to your `INSTALLED_APPS` and `{% load googletags %}` in your template.

This will add two template tags for creating login and logout urls (`google_login_url` and `google_logout_url`). Both tags can take an optional parameter specifying the redirect URL after login/logout:
```
<div class="login">
  {% if user.is_authenticated %}
    Welcome, {{ user.email }}
    <a href="{% google_logout_url %}">Logout</a>
  {% else %}
    <a href="{% google_login_url request.get_full_path %}">Login</a>
  {% endif %}
</div>
```

## Distinguishing auth methods in hybrid mode ##

In order to distinguish whether the user logged in via Google or Django and generate the correct logout link you'll need to add the 'google\_user' context\_processor to your settings:
```
TEMPLATE_CONTEXT_PROCESSORS = (
   ...
   'ragendja.auth.context_processors.google_user',
   ...
)
```

Then you can generate the correct logout link with this code:
```
<div>
...
{% if google_user %}
  <a href="{% google_logout_url %}">Logout</a>
{% else %}
  <a href="{% url django.contrib.auth.views.logout %}">Logout</a>
{% endif %}
...
</div>
```