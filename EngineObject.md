# Engine Object

Engine Objects are objects that have their implementation details hidden within
the engine.

## IDs

Every engine object you interact with is assigned a 32-bit ID. The topmost byte
of this ID is used to store the object type and the 3 lowest bytes store the
object's ordinal.

Type Byte | Purpose
----------|---------
`0x00`    | Reserved for invalid/uninitialized objects.
`0x01`    | Renderer Aspect Ratio objects.
`0x02`    | Renderer Font objects.
`0x03`    | Data system [Store](data/Store) objects.
`0x04`    | Data system [Bundle](data/Bundle) objects.
`0x05`    | KeyBind objects.
`0x06`    | Console Command objects.
`0x07`    | Renderer [Texture](render/Texture) objects.
`0x08`    | Renderer Shader objects.
`0x09`    | Renderer ShaderProgram objects.
`0x0A`    | Renderer Buffer objects.
`0x0B`    | Renderer VertexArray objects.
`0x0C`    | Renderer AnimatedTexture objects.
`0x0D`    | Renderer Camera objects.
`0x0E`    | Audio system Buffer objects.
`0x0F`    | Audio system Source objects.

## Object Pool

All engine objects are stored in object pools. These pools have memory
pre-allocated for a certain number of objects. The ordinal of an object ID is
used as an index into the object pool to retrieve the object's data.

You interact with engine objects via handle-like classes. These classes derive
from the `nwge::Object` class, defined in the `<nwge/object.hpp>` header.

Object pools handle reference counting of the objects contained within. When you
use the copy constructor of an object's handle, you just increment the internal
reference count and no actual copy of the data is made. The object is only
internally deleted once the reference count reaches 0.

Uninitialized object handles default to the ID `0x00FFFFFF` regardless of what
the object handle is.
