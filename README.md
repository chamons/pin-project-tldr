# Pin Projection tl;dr;

Pinning and pin projection are concepts that difficult to understand in async rust.

This repo contains a brief description and some examples for me to look back on as needed.

## Description

Async rust requires self referential data types (pointers to parts of itself). The problem with those is that if you move them, you invalidate those pointers. This would get us into the land of undefined behavior.

The way you know if you need to care about any of this is the Unpin auto trait. Unpin says you can be moved around in memory safely, this includes most types. Structs that are self referential are !Unpin, such as async {} (borrows across await points).

To be able to use a !Unpin type in a context that care about pins, you need to pin it, which prevents it from being moved around in memory.

You can do by:

- Eating the heap allocation and using `Pin<Box<T>>` to keep it on the heap unmoving (heap pinning)
- Using pin! macro to keep it on the stack and not moving

By default the entire type is pinned when you do this. However, you might want to only pin a portion of the type. You do this via "pin-project" crate, which uses the `#[pin_project]` and `#[pin]` macro to generate the required gnarly code safely.

The most common need for this magic is to poll futures by hand or writing your own futures.

## Documentation
- https://fasterthanli.me/articles/pin-and-suffering
- https://doc.rust-lang.org/std/pin/index.html
- https://blog.yoshuawuyts.com/safe-pin-projections-through-view-types/