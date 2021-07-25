# Pipelining

Pipelining is syntax sugar for having the left-hand side receiver 'piped' into a
function as the first argument. The syntax for pipelining is `obj.|func(args*)`,
which is the same as `func(obj, args*)`. Pipelining can also be used with
qualified access, as in `obj.|Utils.func(args...)`.

## Extension Functions

A common use of pipelining is to allow functions to be used on an object in the
same way as methods even if that object is defined elsewhere, similar to
extension functions. However, pipelining avoids two of the key issues with this:

 - Extension functions are merged with namespace of the object, which can
   conflict with existing methods, methods on subtypes, or methods added in the
   future as the API of the object changes.
 - Extension functions use static dispatch, not dynamic dispatch like methods.
   This is a subtle but critical difference in certain situations.

> TODO: Consider requiring an `@Extension` annotation on functions to be used
> with pipelining (and potentially restrict to only pipelining).

> TODO: With the above, consider automatically adding any `@Extension` functions
> to the unqualified scope for pipelining. This, however, would make it harder
> to identify where an extension function is defined without the use of an IDE.
