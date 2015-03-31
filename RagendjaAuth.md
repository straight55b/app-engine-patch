# Module: ragendja.auth.decorators #

## @staff\_only ##

The `@staff_only` view decorator only renders the view if `request.user.is_staff` is `True`. Otherwise it renders a `no_access.html` template (which you have to create yourself).


# Module: ragendja.middleware #

## LoginRequiredMiddleware ##

This saves you from typing `@login_required` all the time.

**Example:**
```
MIDDLEWARE_CLASSES = (
    ...,
    'ragendja.middleware.LoginRequiredMiddleware',
)
LOGIN_REQUIRED_PREFIXES = (
    '/some/path/',
    '/other/path/',
)
NO_LOGIN_REQUIRED_PREFIXES = (
    '/some/path/without_login/',
)
```
With this you require login for all paths that start with `'/other/path'` or `'/some/path/'` except for `'/some/path/without_login/'`. IOW, `NO_LOGIN_REQUIRED_PREFIXES` overrides `LOGIN_REQUIRED_PREFIXES`.