# Module: ragendja.dbutils #

## get\_object(model, ...filters... or key or key\_name or id) ##

Gets an entity from the datastore and returns it. You can specify a key, key\_name or filter rules and they'll be translated to `get()`, `get_by_key_name()`, or `filter(...).filter().....get()`.

Examples:
```
item = get_object(MyModel, key)
item = get_object(MyModel, key_name='somekey')
item = get_object(MyModel, id=5)
item = get_object(MyModel, 'title =', 'ABC', 'counter >', 5)
```

Advantages:
  * it won't raise an exception if the key is invalid
  * instead of doing multiple `filter()` calls you can specify all filters in one single call

## get\_object\_list(model, ...filters...) ##

This works like `get_object`, but it returns a list of entities matching the filter rules:

```
# Fetch 10 entities
items = get_object_list(MyModel, 'title =', 'ABC', 'counter >', 5).fetch(10)
```

## get\_object\_or\_404(model, ...filters... or key or key\_name or id) ##

This does the same as `get_object`, but raises an `Http404` if no object could be found.

## get\_list\_or\_404(model, ...filters...) ##

This does the same as `get_object_list`, but raises an `Http404` if no objects could be found.

## @transaction ##

`@transaction` is a simple wrapper for db.run\_in\_transaction.

**Example:**
Instead of writing `db.run_in_transaction` all the time:
```
def do_something(...):
    ...

def view1(request, ...):
    ...
    db.run_in_transaction(do_something, ...)
    ...

def view2(request, ...):
    ...
    db.run_in_transaction(do_something, ...)
    ...

```
You can just write this:
```
@transaction
def do_something(...):
    ...

def view1(request, ...):
    ...
    do_something(...)
    ...

def view2(request, ...):
    ...
    do_something(...)
    ...
```

## db\_create(model, ...attrs...) ##

This function safely creates a new entity with a random key\_name.

**Example:**
```
item = db_create(MyModel, title='hey', index=4)
# item.key().name() could be "a1K6AMAo55BepDcx"
```

## db\_add(model, key\_name, ...attrs...) ##

This function adds an entity to the datastore if it doesn't exist. Otherwise it returns None. Note that this function automatically runs in a transaction.

**Example:**
```
item = db_add(MyModel, 'some_key_name', title='hey', index=4)
if not item:
    # entity with that key_name already exists!
else:
    # we've added a new entity
```

## generate\_key\_name(...parts...) ##

This generates a `key_name` from the given strings. It automatically prefixes the key with a 'k', so it's guaranteed to be valid and separates all parts with a '/' (while escaping the separator in each part, so it's a non-ambiguous transformation):
```
>>> generate_key_name('abc', 'def')
'kabc/def'
```

## to\_json\_data(entity\_or\_list, property\_or\_properties) ##

Converts a model into dicts for use with `JSONResponse`/`simplejson.dumps()`.

You can either pass a single model instance and get a single dict or a list of models and get a list of dicts. After that you can still modify the data because it's a Python object. When you're finished you can use, for example, `JSONResponse` to return the data in JSON format.

For security reasons only the properties in the property\_list will get added. If the value of the property has a json\_data function its result will be added, instead.

### KeyListProperty ###

Simulates a many-to-many relation by storing a list of Key instances. When displaying this property in a ModelForm you get a nice selector interface like for Django's ManyToManyField.

```
class File(db.Model):
   ...

class Folder(db.Model):
    files = KeyListProperty(File)
```

## prefetch\_references(entity\_list, property\_or\_properties) ##

Dereferences the given (Key)ReferenceProperty fields of a list of objects in as few get() calls as possible:
```
class File(db.Model):
    creator = db.ReferenceProperty(User)

files = File.all().filter(...).fetch(20)
# The following will dereference all creators in a single get().
# If two files have the same creator the creator will only be retrieved once!
prefetch_references(files, 'creator')
for file in files:
    # The following will not result in a db access because the creator has
    # been prefetched.
    name = file.creator.get_full_name()
```

## cleanup\_relations ##

This is a signal handler which can be used in order to automatically delete an entity and all its related entities.

Example:

```
from django.db.models import signals
from ragendja.dbutils import cleanup_relations

class User(db.Model):
    ...

class File(db.Model):
    owner = db.ReferenceProperty(User)

signals.pre_delete.connect(cleanup_relations, sender=User)
```

From now on, a user instance gets deleted together with all related files. Note that signals only work if you use model instances.

This feature provides a weak form of referential integrity, but it won't work for entities that get used by lots of users simultaneously because, in that case, the list of referencing entities could change while it's being collected.