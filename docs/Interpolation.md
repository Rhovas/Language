# Interpolation

Rhovas uses interpolation for accessing values in three contexts:

 - [Strings](Literals.md#string): `"name = ${name}"`
 - [Patterns](Pattern Matching.md#interpolation): `[x, ${y}, z]`
 - [Syntax Macros](./Macros.md#syntax-macros): `#sql { SELECT * FROM ${table} }`

Strings and patterns both use `${expr}`, as will many of the provided DSLs where
interpolation is added (languages with existing interpolation should use it).
Consistency of syntax between strings, patterns, and common DSLs is an important
consideration for interpolation.

> TODO: Consider supporting `$name` for variables, which simplifies common cases
> and can improve readability. However, this could also hurt readability in some
> situations, such as `"$a_b-c + x.$y.z = \$50.0"`.

> TODO: Consider alternative syntax options such as `#{}` (which is consistent
> with syntax macros) or `\{}` (which is consistent with string escapes). A few
> points to consider:
> 
>  - The simplified `$name` form works best for `${}`, as `\name` may conflict
>    with string escapes while `#name` conflicts with macros.
>  - Escaping interpolation may also be needed, especially for DSLs. For this,
>    `\${}` or `\#{}` are possible, while `\\{}` is trivially correct.
