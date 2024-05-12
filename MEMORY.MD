# Memory Allocation

Since you're working in C++, you will have to deal with all the hardships of
manual memory management.

## Table of Contents

- [Memory Allocation](#memory-allocation)
  - [Table of Contents](#table-of-contents)
  - [Available Allocators](#available-allocators)
  - [Scratch-space](#scratch-space)

## Available Allocators

Every C++ programmer is aware of the usual memory management semantics:

```c++
void someFunc(size_t count) {
  // dynamically allocate an array of integers
  int *someArray = new int[count];

  /// ... pretend we do something with that array here ...

  delete[] someArray;
}
```

This is nice, but problematic in specific scenarios.

Because nwge is a shared library, there is no guarantee that the memory
allocator nwge is using is the same one your app is using.

That means passing any kind of pointer between nwge and your app gets messy.

For example, you must pass a `nwge::State` pointer to the `nwge::start` method.
This pointer is managed by the engine from that point onwards.

```c++
#include <nwge/state.hpp>

int main(int argc, char **argv) {
  // both of these are equivalent
  nwge::start<MyState>();
  nwge::startUnchecked(new MyState());
  // this is also common
  nwge::startUnchecked(MyState::create());
}
```

The pointer is later `free`d by the engine. Here's our problem: If one allocator
creates a pointer, another allocator cannot free it, causing a crash.

On Windows, your app is likely using UCRT's allocator, whereas nwge uses
[mimalloc][mimalloc] internally. This is the perfect setup for this crash to
occur.

Nwge checks that the pointer you pass was allocated by the engine's allocator.
That is still a crash, but it's also likely how you ended up here.

Here's how to avoid passing bad pointers to nwge:

1. Allocate everything with `nwgeAlloc` manually.

   This is generally not recommended, but definitely a solution if you wish to
   avoid allocating everything else via nwge's allocator. This allows more
   flexibility with your memory allocation, but is usually not worth it.

   Note that this does not invoke a constructor for your class, since it is
   effectively just `malloc`.

   ```c++
   #include <nwge/state.hpp>
   #include <nwge/common/allocator.h>

   int main(int argc, char **argv) {
     // allocate the memory for MyState
     auto *state = reinterpret_cast<MyState*>(nwgeAlloc(sizeof(MyState)));
     // construct in-place
     new(state) MyState();
     // then pass the pointer to nwge
     nwge::startUnchecked(state);
   }
   ```

2. Use nwge's allocator override header.

   This is preferred as it is much cleaner and allows you to utilize basic C++
   features fully.

   The `<nwge/common/allocatorOverride.hpp>` header defines override functions
   which replace the default `operator new`/`operator delete` with ones that
   call nwge's memory functions instead.

   You must include the header in exactly one single code file. If you do, the
   memory allocator will be replaced in your entire program.

   This header is included by the `<nwge/engine.hpp>` header, which you
   should always include exactly once in your main file. If you include
   `engine.hpp`, you must not include `allocatorOverride.hpp`.

   ```c++
   /*
   If you want to avoid engine.hpp for some odd reason:
   #include <nwge/state.hpp>
   #include <nwge/common/allocatorOverride.hpp>
   */
   #include <nwge/engine.hpp>

   int main(int argc, char **argv) {
     // no more crash!
     nwge::start<MyState>();
     nwge::startUnchecked(new MyState());
   }
   ```

A pattern to note is to use a static `create` method on your `State` subclass
which will allocate the memory for you. Below is an example of how one could
switch between the two approaches with a preprocessor macro.

```c++
/* --  MyState implementation file  -- */

// assume we have some config.h file that defines C_STYLE_ALLOC to 0 or 1
#if C_STYLE_ALLOC
  #include <nwge/common/allocator.h>
#endif

MyState *MyState::create() {
  #if C_STYLE_ALLOC
    auto *ptr = reinterpret_cast<MyState*>(nwgeAlloc(sizeof(MyState)));
    new(ptr) MyState;
    return ptr;
  #else
    return new MyState;
  #endif
}

/* --  Main file  -- */

#include <nwge/state.hpp>
#if !C_STYLE_ALLOC
  // even if we include it here, it will still affect MyState::create
  // we just have to ensure we include it only once
  #include <nwge/common/allocatorOverride.hpp>
#endif

int main(int argc, char **argv) {
  // both version will work now, regardless of C_STYLE_ALLOC's value
  nwge::startUnchecked(MyState::create());
}

```

## Scratch-space

In case you need some temporary memory to work and especially if you don't want
to free it later, the scratch-space is perfect for you.

The scratch-space allocator uses a pre-allocated memory buffer and therefore
does not require memory allocated from it to be freed. You do, however, have a
limited amount of memory before previous allocations start getting overwritten.
Hence it is recommended to use scratch-space sparingly and with small amounts of
data. If you wish to know the size of the scratch-space, see the
`NWGE_SCRATCH_SPACE_SIZE` constant in the `engineLimits.h` header.

The scratch-space allocator provides both the `nwgeScratchAlloc()` and the
`nwgeScratchRealloc()` functions to use with allocator-aware containers.
`ScratchArray` and `ScratchSlice` are partial specializations of the respective
containers but with the usual engine allocator swapped out for the
`ScratchAllocator` instead.

An example of where scratch-space is used is the JSON encoder, where the JSON
data is built inside a `ScratchSlice` before being copied into a regular,
heap-allocated `String`.

An advantage of scratch-space is that both allocation and reallocation are
really fast. In case you allocate some memory from the scratch-space and wish to
expand it, it is very likely that it will be expanded in-place. Matter of fact,
expanding an allocation is guaranteed to be in-place if you never call
`nwgeScratchAlloc()` between the allocation and the re-allocation.

Another note is that buffer overflows are much less likely to be lethal, as all
the scratch-space memory is completely contiguous.

Below is an example, taken straight from nwge's JSON encoder:

```c++
using Buf = ScratchSlice<char>; // slice stored in scratch-space

static void encodeNumberTo(Buf &out, f64 number) {
  // the integer value of the number
  f64 integer;
  // the fractional value of the number
  f64 fraction = std::modf(number, &integer);

  usize bufSize;
  // buffer storing the final encoded string
  CStr buf;
  // fraction is 0 - the number is an integer
  if(fraction == 0.0) {
    // calculate buffer size
    s32 size = std::snprintf(nullptr, 0, "%d", s32(integer));
    // in case of errors, exit
    if(size <= 0) {
      return;
    }
    bufSize = usize(size) + 1;
    buf = reinterpret_cast<CStr>(nwgeScratchAlloc(bufSize));
    // format as integer
    std::snprintf(buf, bufSize, "%d", s32(integer));
  } else /* number has fractional value */ {
    s32 size = std::snprintf(nullptr, 0, "%f", number);
    if(size <= 0) {
      return;
    }
    bufSize = usize(size) + 1;
    buf = reinterpret_cast<CStr>(nwgeScratchAlloc(bufSize));
    // format as float
    std::snprintf(buf, bufSize, "%f", number);
  }

  // append to output buffer
  out.append(ArrayView<const char>{buf, bufSize - 1});
  // Because both buf and out are allocated in scratch-space, cache locality
  // for the copy in append is improved.
}
```

[mimalloc]: https://github.com/microsoft/mimalloc
