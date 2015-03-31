# Module: ragendja.sites.dynamicsite #

## DynamicSiteIDMiddleware ##

This middleware makes it possible to host multiple websites with a single Django instance by automatically setting your `SITE_ID` based on the current request's domain. Just use the admin interface to add your sites and the corresponding `SITE_ID` will be set automatically for you. As a fallback, 'www.' is removed if the request's domain starts with 'www.' or 'www.' is added if the request's domain doesn't start with 'www.'.

By default, if no site could be found the middleware will create one for the request's domain, automatically. You can turn this off in your settings:
```
CREATE_SITES_AUTOMATICALLY = False
```

If you specify a `SITE_ID` in your settings it has to be the `str(site.key())` of the default site which is used as a fallback if no site could be found.

# Module: ragendja.testutils #

## ModelTestCase ##

This provides an easy way to validate the contents of the DB. You have to specify the model to validate against with the `model` attribute. From then on you can call `validate_state()` to check if a certain function modifies the DB contents correctly:

**Example:** The following validates that the table contains exactly two rows and that their 'a', 'b', and 'c' attributes are 1, 2, 3 for one row and 11, 12, 13 for the other row. The order of the rows doesn't matter.
```
class MyTestCase(ModelTestCase):
    model = MyModel

    def test_valid_function():
        # Do something that changes the DB
        change_something()
        # Now check if that function worked correctly
        self.validate_state(
            ('a', 'b', 'c'),
            (1, 2, 3),
            (11, 12, 13),
        )
```

# Module: appenginepatcher #

You can find out whether you're running on the production server:
```
from appenginepatcher import on_production_server
if on_production_server:
    # we're on the production server...
else:
    # dev_appserver...
```

You can also retrieve the project's appid:
```
from appenginepatcher import appid
```