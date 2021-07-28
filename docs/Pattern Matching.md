# Pattern Matching

Pattern matching in Rhovas supports a powerful destructuring system that can be
applied in match cases, variable declarations, or function arguments. Patterns
should work identically in each use case, accounting for some features which
may not be applicable (such as matching on the type in a variable declaration).

## Context

The location of a pattern determines the effect of a match and may restrict the
features available in patterns. However, all patterns themselves should have the
same behavior in all contexts.

 - **Match Cases** follow standard behavior, executing the case statement on a
   match or continuing to the next case on failure (else erroring).
    - `0`, `[1, 2, 3]`, `Point {x: 1, y: 2, z: 3}`
 - **Variable Declarations** act as assertions (`assert`), throwing an error if
   the match fails. The pattern is an optional name and/or optional destructure
   with at least one variable binding in total. The type of the root pattern is
   statically verified with the value assigned.
    - `val name = obj;`, `val name: Type = obj;`, `val [x, y, z] = list;`
 - **Function Arguments** act as preconditions (`require`), throwing an error if
   the match fails.
    - For named functions, the pattern must have at least one variable binding
      and all bindings must be provided a type (either directly or indirectly
      through inference).
       - `func(Point {x, y, z}) { ... }`, `sqrt(n: Integer ${n > 0}) { ... }`
    - For lambdas, the pattern has no restrictions (variable bindings are not
      required as arguments may be unused or expected to match specific values,
      and types are not required as they can be inferred).
       - `object.map |key, value| { ... }`, `list.sort |{name, *}| { ... }`

> TODO: Consider supporting dynamic dispatch for functions with patterns (thus
> allowing top-level values).

## Literals

Patterns can use all literals in Rhovas (`null`, booleans, integers, decimals,
strings, atoms, lists, and objects). Lists and objects are effectively handled
through destructuring, but share the same syntax and behavior.

 - `[true, 1.0, {key: "value"}]`

## Destructuring

Rhovas has three methods of destructuring objects:

 - **Ordered Destructuring** uses the sequential ordering of components in an
   object, such as the elements of a list or the order of fields in a struct.
    - `val [x, y, z] = Point(1, 2, 3);`
 - **Named Destructuring** uses the named components in an object, such as the
   key-value pairs of a map or the fields of a struct.
    - `val {x, y, z} = Point(1, 2, 3); //x = 1, y = 2, z = 3`
    - `val {x, y: y2, z: 1} = Point(1, 2, 3); //x = 1, y2 = 2`
 - **Typed Destructuring** uses the type of an object, acting like an `is` check
   that can be combined with other patterns.
    - `match (obj) { Point: ... else: ... }`
    - `match (obj) { Point {x, y, z}: ... else: ... }`

> TODO: Decide how each form of destructuring fits into the type system.
>
>  - Ordered destructuring can be applied to any `Iterable` (which would allow
>    for destructuring infinite sequences) or struct.
>  - Named destructuring can be applied to any struct, which includes object
>    literals. Supporting a `Map` interface would require handling the case of
>    keys that aren't `Atom`s, like `String`s. It maPossibly `Map<Atom, ?>` when statically known?
>  - Typed destructuring can be applied to any struct. Supporting arbitrary
>    objects is likely possible but may not be the best design decision.

> TODO: Consider supporting syntax to bind _and_ destructure a value into
> components when both are needed. One possibility is to have the destructure
> immediately following the binding, as in `list [x, y, z]`. Typed destructures
> benefits from this the most as it allows binding a variable of the matched
> type, as in `p Point`.

> TODO: Decide whether typed destructuring is ordered, named, or some type of
> mix. Since the fields are known, repeating the name is unnecessary and hurts
> readability for nested patterns. A few options:
>
>  - Only support one of ordered/named destructuring. Ordered only would likely
>    be more restrictive with varargs as it would return a list of field values
>    instead of an object including field names.
>  - Allow both ordered and named destructured, with ordered being preferred in
>    the general case as it's easier to work with.
>  - Use a custom approach for types, such as combining both ordered and named
>    destructuring but requiring any bindings to match the corresponding field
>    name (so `Point {z, y, x}` is an error). However, this limits matching on
>    specific fields (especially when combined with varargs like `rest*`).

Additionally, there are a few other common destructuring features included.

 - **Ignored Values** are represented using `_` and are not bound to a variable.
    - `val [x, _, z] = Point(1, 2, 3); //x = 1, z = 3`
 - **Vararg Values** are representing using either `*` (zero or more) or `+`
   (one or more), which produce a list/object value as appropriate. If the
   vararg value is ignored, the `_` can be left out as a shorthand.
    - `val [x, yz+] = Point(1, 2, 3); //x = 1, yz = [2, 3]`
    - `val {x, *} = Point(1, 2, 3); //x = 1`

> TODO: Consider allowing varargs to be used anywhere in a list, such as
> `[first, middle*, last]`.

> TODO: Consider allowing multiple varargs in a list. This would need defined
> behavior for lazy vs greedy matching and/or should error on potentially
> ambiguous patterns such as `[start*, middle, end*]`.

> TODO: Consider allowing infinite sequences with varargs (which relates to the
> type system for objects supporting pattern matching).

## Interpolation

Interpolation is used to access values or perform computation in Rhovas from
within a pattern, and is used for two purposes:

 - **Variable Access** allows matching on the value of a variable rather than
   introducing a new binding.
    - `[x, ${y}, z] = Point(1, 2, 3); //matches when y == 2`
 - **Predicates** allow arbitrary expressions to be used within a pattern and
   are located after the pattern. They can be applied to specific bindings or
   the entire pattern depending on what variables are needed.
    - `[x, y ${y > 0}] = Point(1, 2, 3); //matches as y == 2`
    - `[x, y, z] ${x == z} = Point(1, 2, 3); //fails as x != z`

> TODO: Resolve [Interpolation](Interpolation.md) TODOs for syntax.

> TODO: Consider restricting predicates to being pure functions, thus avoiding
> side effects and allowing optimizations on the order of pattern evaluation.

