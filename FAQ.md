## Why do you use a zipped Django package? ##

The most important reason is that a zipped Django package loads twice as fast as an unzipped Django package. Also, by zipping Django you have more space for your own files. Actually, we consider using some automated procedure to automatically zip your whole application into blocks of 10MB each before uploading it to the production server.

## Should I use app-engine-patch or django-helper? ##

app-engine-patch is much more complete and more active than django-helper. Also, it's much easier to use (just download and unzip - no need to make a Django package yourself). There really is no reason to use django-helper and follow complicated installation steps.

## Now that Django 1.0 is integrated in App Engine should I still use app-engine-patch? ##

Yes! The Django 1.0 version that is integrated in App Engine does **not** run out-of-the-box! Basically, you only get the template system. You can't use django.forms, django.contrib.auth, django.contrib.admin, and so on. Also, load times aren't faster because only a minor part of Django is pre-loaded into memory. You still have to load the rest which is what [slows it down](http://code.google.com/p/googleappengine/issues/detail?id=1695). Actually, our zipped Django package might even load faster than App Engine's unzipped package.

## Is there a full-text search solution for App Engine? ##

You can use [gae-search](http://gae-full-text-search.appspot.com/) which is free for non-commercial use. It's still affected by datastore limitations, but it also provides a few workarounds for them. In the admin interface you can then enable full-text search on a SearchIndexProperty (e.g., named "index") like this:

```
class PersonAdmin(admin.ModelAdmin):
    search_fields = ['@index']
admin.site.register(Person, PersonAdmin)
```

## Why does a response take so long? ##

Unless you've done something ugly in your code, this should only happen when a new Django instance gets started. On average, this can take between 1s and 4s or even more, depending on the complexity of your website. After that, all subsequent requests to that instance should be fast (100-350ms or less if you don't render templates, but only return a JSON response).

When you have no traffic on your site the Django instance gets shut down very quickly (in a few seconds). Now, the problem is that if your site doesn't get enough traffic your Django instances will get shut down too quickly and your visitors will invoke a new Django instance with almost every request. In this case, the site will seem to be very slow and requests will always take a few seconds.

So, if you want to build a heavy site for just a handful users Django might not be the best choice, right now, but Google is working on a solution for the slow instance startup problem. The two main reasons for slow startups are:
  * Python code isn't yet compiled into bytecode
  * not all Python modules support being loaded from a zip file (zip files speed up file loading)

For now, this means: if you can [zip your code](ZipPackages.md) do it!