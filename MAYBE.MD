# The `Maybe<T>` type

For a given type `T`, consider the following table.

  Nullability     |   Owning   | Non-owning
:----------------:|:----------:|:----------:
 **Non-nullable** |     `T`    |    `T&`
   **Nullable**   | `Maybe<T>` |    `T*`

The above table is assumed within all of nwge's code. Whenever you see a
function return a pointer, *always* check whether that pointer is a `nullptr`.
If a function returns a reference instead, you can be sure it is *never* null.
In either case you *do not* own the memory returned to you. In case you are
responsible memory, the function will return a `Maybe<T>` or just the object
itself.

## `Maybe` vs `std::optional`

Having a owning nullable type is nice, but doesn't `std::optional` already do
that for us?

Well, yes, but:

* We can never be certain of the exact implementation used by `std::optional`.
  For example, Clang's implementation of `std::optional` is just a pointer.

* The new `Maybe` implementation introduced in v0.6 *does not allocate memory*.
  In previous versions, `Maybe` was allocator-aware as it had to allocate and
  de-allocate memory for the underlying pointer. The new pointer simply contains
  an array of bytes of the size of the object it holds.

Expanding upon that second point: Let's say that `sizeof(T)` is 16. The `Maybe`
will then contain an array of 16 bytes where the object will be stored, *if the
`Maybe` contains an object.* In case the `Maybe` does not contain an object,
that memory will simply stay uninitialized. This allows us to store objects that
are not otherwise default constructible without using a pointer. Not using a
pointer means there is no necessity to ever allocate or de-allocate memory. It
is always fastest to just not allocate any memory in the first place.

## Outside C++

Note that many other languages implement optional types directly within the
language itself, alongside regular pointers. An example of such a language would
be Zig, which supports both plain old pointers and proper optional types.
