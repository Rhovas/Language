# Literals

Rhovas supports many of the standard literal types seen in other languages:

 - [Null](#Null): `null`
 - [Boolean](#Boolean): `true`, `false`
 - [Integer](#Integer): `42`, `0xFFA500`
 - [Decimal](#Decimal): `101.5`, `6.022e23`
 - [Character](#Character): Not supported, included for documentation
 - [String](#String): `"string"`, `"\n\r\t"`, `"val = ${val}"`
 - [Atom](#Atom): `:name`
 - [List](#List): `[x, y, z]`
 - [Object](#Object): `{x: 1, y: 2, z: 3}`, `{x, y, z}`

## Null

Null literals are just that - `null`.

## Boolean

Boolean literals are `true`/`false` and fall under the `Boolean` type.

## Integer

Integer literals can be expressed in four different bases:

 - Decimal (base-10): `42`
 - Binary (base-2): `0b10111010`
 - Octal (base-8): `0o755`
    - Octal may be rarely used, but it can be helpful when working with other
      systems that use it (such as a script affecting file permissions).
 - Hexadecimal (base-16): `0xFFA500`
    - Digits `A-F` are required to be capitalized to minimize ambiguity with the
      lowercase characters used for base prefixes, type suffixes, or exponents.

> TODO: Consider using separate types depending on the base, thus printing an
> integer literal displays with the correct base.

Leading zeroes are not allowed to avoid confusion with languages that use a 0
prefix for octal numbers.

Many languages allow using underscores to separate digits and aid readability,
such as `0xFF_A5_00`. However, 

> TODO: Specification for where underscores are allowed (consider start/end
> with prefixes, multiple in a row, decimal points & exponents for decimals).

The current preference is to use arbitrary precision for integer literals and
allow optimization where feasible. Thus, there are currently no type suffixes 
for integer (or decimal) literals. The impact on this depends on how the type
hierarchy for numbers is structured and the design of the standard library.

> TODO: Integer types

## Decimal

Decimal literals can be expressed in either decimal or scientific notation:

 - Decimal: `101.5`
 - Scientific: `6.022e23`
    - The `e` must be lowercase as it is not part of the number itself (like
      integer base prefixes) to help distinguish it from the hexadecimal `E`.

Leading zeroes are not allowed, however trailing zeroes (`1.000`) are as they
have an impact on the precision of the number in some languages (such as Java's
[BigInteger](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html)).

> TODO: Consider limiting trailing zeroes (and effects of precision) to decimals
> in scientific notation? Likely not beneficial and more prone to confusion.

Other features like underscores and type suffixes work the same as with integer
literals (most of which are awaiting specifications, see the [Integer](#Integer)
section for details).

# Character

Character literals are *not* supported in Rhovas, but this section is included
for sake of documentation.

The concept of a character is not well-defined and has different meanings in
many languages. The primary distinction is whether a character is something that
is visually one 'thing' (as in [Swift](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html#ID293))
or is unicode-wise one 'thing' (as in [Rust](https://doc.rust-lang.org/std/primitive.char.html)).
Even within this there is more complexity, such as Rust using Unicode scalar
values instead of Unicode code points (see the previous link for definitions).

The current approach is to maintain separate types for working with either
graphemes or code points, which can be initialized from a String literal.

> TODO: Grapheme vs code point types (and exact definitions)

# String

String literals use double quotes and work the same as in most languages. Single
quoted strings are not supported - having both is generally used for templating
purposes (like HTML attributes) and Rhovas contains other features that are
better suited for that behavior.

Strings support the following escape characters:

 - Newline: `\n`
 - Carriage Return: `\r`
 - Horizontal Tab: `\t`
 - Double Quote: `\"`
 - Dollar Sign: `\$` (used for interpolation)
 - Backslash: `\\`
 - Unicode (UTF-8): `\uXXXX`

> TODO: Consider support for octal escapes `\oXXX`, since octal integer literals
> are supported. Alternatively, consider removing octal integer literals.

> TODO: Consider support for UTF-16 escapes, potentially `\uuXXXXXXXX`? If `\`
> is used for interpolation too, Swift's approach of `\u{integer}` may also be
> a good option (though may imply that the escape happens at runtime).

Interpolation is also supported using `${expr}` and can be escaped with `\$`.

> TODO: Resolve [Interpolation](Interpolation.md) TODOs for syntax.

Triple-quoted strings are commonly used for raw or multiline string literals.
They are currently not supported in favor of using a syntax macro, such as
`#string { ... }`, when needed.

> TODO: This is a good example of where arguments on syntax macros could be
> useful, such as `#string(:raw) { ... }` to disable escaping. This would also
> handle different degrees of escaping better, such as disabling simple escapes
> like `\n` while still allowing `${expr}` if needed.

# Atom

Atoms represent the runtime value of identifiers in the language. They use a
colon prefix, as in `:name`, and support the same identifiers as the language
itself (but can be created from any string as needed).

> TODO: The syntax for identifiers is largely dependent on atoms, as they should
> support handling identifiers in other languages. Atom literals should support
> the basic formats used, which appears to be letters, digits (excluding the
> start), underscores, and hyphens.

# List

List literals are used for ordered sequences of values. As a list, values do not
need to be homogenous like arrays but may not (currently) retain type
information like tuples. The constructed list is immutable.

> TODO: Consider supporting type inference into arrays or tuples. This may have
> complex coercion behavior, but is likely similar to what would be done with
> integer/decimal literals.

The base syntax is `[x, y, z]`, using square brackets for the list and comma
separated values.

> TODO: Consider supporting a 'spread' operator for merging lists? Concatenation
> is probably sufficient like `list + [x]`, but merging object literals is also
> a common method. This is most dependent on whether a spread operator would
> already exist for other features, such as splicing function arguments.

# Object

Object literals are used for named key-value pairs, however the order of entries
is defined to be insertion order to ensure consistent behavior with iteration
and printing (primarily for debugging). The constructed object is immutable.

The base syntax is `{x: 1, y: 2, z: 3}`, using curly braces for the object, a
colon to separate the key and value, and commas between each pair. Keys are
always atoms and can be accessed directly (`obj.key`) or through indexing
(`obj[:key]`). If the key and value are the same identifier, as in `{x: x}`, the
shorthand `{x}` may be used instead.

> TODO: Consider supporting syntax for arbitrary keys via atoms and/or strings,
> such as: `[:atom]: value` or `["string"]: value`.

> TODO: Consider allowing values to access existing keys on the defined object,
> such as `{x: 1, y: x + 1}`. If so, how does this interact with scope?

> TODO: Consider supporting a 'spread' operator for merging objects? As with
> lists, functions can work in simple cases like `obj.with({x: 1})`.
> 
> Alternatively, perhaps `+` has reasonably defined behavior for objects through
> concatenation as a parallel to lists.

Object literals also apply to struct initialization, which is effectively a
named object literal as in `Point {x: 1, y: 2, z: 3}`. Since the structure of
the object is known, the syntax `{a, b, c}` can also be used with values to mean
`{x: a, y: b, z: c}`.

> TODO: Consider requiring consistent order of keys with struct initialization,
> especially for something like Point {1, x, 3}. This impacts destructuring as
> well as it mixes ordered and named behavior.
