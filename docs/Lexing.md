# Lexing

This document outlines the lexer rules for Rhovas. The [Literals](Literals.md)
page contains related information for integers, decimals, strings, and atoms.

## Encoding

The default encoding is UTF-8.

> TODO: Determine what consequences this has, and whether other encodings should
> (or must) be supported.

## Whitespace

Rhovas is not a whitespace sensitive language, and thus whitespace does not play
 a role beyond separating tokens. Rhovas recognizes `[ \t]` as whitespace and
`\n\r?|\r\n?` as newlines.

> TODO: Consider allowing significant whitespace/indentation, as discussed in
> [Issue #2](https://github.com/Rhovas/Language/issues/2).

## Comments

Comments are still a work in progress, but to allow at least some degree of
commenting in code `//` is used as an inline comment which continues through to
the end of the line. Comments are not currently emitted as a token.

> TODO: Comment specification (especially doc comments).

## Identifiers

Identifiers must start with one of `[A-Za-z_]` and can contain the characters
`[A-Za-z0-9_]`, which supports most of the common identifier formats in use.

> TODO: Consider including `-`, which is a common separator in web development.

## Numbers

Numbers start with `[0-9]` and follow the standard lexing approach. However, for
decimals the period is only lexed as a decimal point when followed by a digit,
as in `1.0` and not `1.toString()`.

## Strings

Strings start with `"` and are currently emitted as a single token (which does
not address interpolation). An unterminated string or invalid escape is
considered an error. See the [String](Literals.md#String) docs for details.

> TODO: Determine how to support string interpolation.

> TODO: Consider handling escapes at the lexer level, which may be slightly more
> performant and easier to work with but prevent recovering the original input.

## Atoms

Atoms work in the same way as identifiers. A leading `:` must be followed by the
start of an identifier to be considered an atom.

## Operators

Tokens that are used directly in the parser (as opposed to using their type)
are considered operators. For simplicity, unknown characters are emitted as
operators (which tends to provide better error messages as well).

> TODO: Consider handling multi-character operators in the lexer. This requires
> establishing rules for what characters can be combined into a single operator
> that avoids parsing conflicts (such as `>` `>` vs `>>` with generics).
