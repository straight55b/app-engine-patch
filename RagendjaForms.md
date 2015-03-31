# Module: ragendja.forms #

## Using FormSets with ModelForm ##

`FormSet`s can be used to automatically display data for other models that have a `ReferenceProperty` back to your parent model (or `SelfReferenceProperty`).

The easiest way to use a `FormSet` as part of a `ModelForm` is to add each FormSet as a `FormSetField` and upgrade your `ModelForm` to a `FormWithSets`.  The `FormWithSets` handles rendering your `FormSets` and makes sure that everything loads and saves correctly.

```
class Person(db.Model):
    father = db.SelfReferenceProperty(collection_name='children_fathered_set')
    mother = db.SelfReferenceProperty(collection_name='children_mothered_set')
    name = db.StringProperty(Required=True)

class PersonForm(forms.ModelForm):
    fathered_children = FormSetField(Person, fk_name='father')
    mothered_children = FormSetField(Person, fk_name='mother')

    class Meta:
        model = Person
PersonForm = FormWithSets(PersonForm)
```

You can use `PersonForm` as if it were a normal `ModelForm`.  `collection_name` and `fk_name` are only required if there are multiple references to the same model class.