# Engine Objects

The engine object system allows for more efficient managing of references by
hiding the implementation details behind a simple ID.

Every object has a 32-bit ID, which itself consists of an object type and an
ordinal number. The topmost byte of the ID determines the type, whereas the
remaining 3 lower bytes are the ordinal number. This allows for fast lookup
without ever confusing objects of different types. Below is a table of the
currently implemented object types (as per `object.hpp`)

 Name         | Number | Description
--------------|--------|-------------
`Reserved`    | 0 | Used only for invalid/uninitialized objects.
`AspectRatio` | 1 | Represents a renderer aspect ratio object.
`Font`        | 2 | Represents a renderer [font][font].
`Store`       | 3 | Represents a data system [data store][store].
`Bundle`      | 4 | Represents a data system [bundle][bundle].
`KeyBind`     | 5 | Represents a key binding.

The object ID `0x00FFFFFF` is used to represent invalid objects.

The `Object` struct from `object.hpp` represents a handle over an object. This
means that when such an object goes out of scope, the object it represented does
not get disposed immediately. Instead, its internal reference count is
decremented. This allows for multiple handles to refer to the same object
without creating multiple copies of the same data. Logically, if the reference
count reaches 0, the object is actually disposed.

All the objects are stored in separate object pools. These internal pools are
limited in size. See `engineLimits.h` for information about their actual sizes.
The population of these pools can be checked with the `e.objects` console
command or with the `Object::query()` function.

Note that an application *cannot* add its own custom objects, hence they are
called engine objects. They are used as interfaces over objects stored
internally in the engine.

[font]: CFN#render-text-fast
[store]: DATA#data-stores
[bundle]: DATA#bundles
