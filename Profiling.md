# Enabling the profiler #

In your `settings.py` just add the following:

```
ENABLE_PROFILER = True
```

# Tuning the results #

The profiler is configured via `settings.py`.

You can restrict the profile to certain functions by specifying a regular expression:

```
PROFILE_PATTERN = '.*token.*' # matches all function names containing 'token'
```

The maximum number of functions can be set with:

```
MAX_PROFILE_RESULTS = 160 # default is 80
```

You can change the sorting criteria with:

```
SORT_PROFILE_RESULTS_BY = 'cumulative' # default is 'time'
```

It's possible to additionally get the callers and callees profile:

```
EXTRA_PROFILE_OUTPUT = 'callers' # or 'callees' (you can also provide a tuple with multiple values)
```

# Profiling under load #

If you profile a website under load your logs will be flooded. You can limit the profiler to just a certain percentage of all requests:

```
PROFILE_PERCENTAGE = 2.5 # Profile only 2.5% of all requests
```

Alternatively, you can limit the profiler to requests that contain "profile=forced" in their query string:

```
ONLY_FORCED_PROFILE = True
```

# How to profile datastore calls #

Put this in your settings.py to profile all important datastore calls.

```
ENABLE_PROFILER = True
SORT_PROFILE_RESULTS_BY = 'cumulative' # default is 'time'
PROFILE_PATTERN = 'ext.db..+\((?:get|get_by_key_name|get_by_id|fetch|count|put)\)'
```

Note that the `db.Model.get()`, `db.Model.get_by_key_name()`, `db.Model.get_by_id()`, and `db.Model.put()` calls translate into `db.get()` and `db.put()`, so for every call of these model functions you'll also get a call of the global db functions.

Please take a look at the `ragendja.dbutils.prefetch_references()` function (see the [documentation](Documentation.md)). It can help optimize your code.