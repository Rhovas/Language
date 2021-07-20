# Literals

Rhovas supports many of the standard literal types seen in other languages:

 - [Null](#Null): `null`
 - [Boolean](#Boolean): `true`, `false`
 - [Integer](#Integer): `42`, `0xFFA500`
 - [Decimal](#Decimal): `101.5`, `6.022e23`

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
