In app-engine-patch we've integrated a remoteapi handler, so **admins** with a Google Account (Google Apps/Domain accounts don't work!) can run manage.py syncdb, manage.py shell, and other commands on the production server:

```
manage.py shell --remote
>>> from myapp.models import Person
>>> Person.all()[0].delete() # deletes a person from the production datastore!
```

All datastore access will be routed to the production server, so you can do migrations or clean up your data, for example. Read the [remote\_api article](http://code.google.com/appengine/articles/remote_api.html) for more information.

# Settings #

The following settings are available:

```
DATABASE_OPTIONS = {
    # Override remoteapi handler's path (default: '/remote_api').
    # This is a good idea, so you make it not too easy for hackers. ;)
    # Don't forget to also update your app.yaml!
    'remote_url': '/remote-secret-url',

    # !!!Normally, the following settings should not be used!!!

    # Always use remoteapi (no need to add manage.py --remote option)
    'use_remote': True,

    # Change appid for remote connection (by default it's the same as in your app.yaml)
    'remote_id': 'otherappid',

    # Change domain (default: <remoteid>.appspot.com)
    'remote_id': 'otherappid',
}
```