# Expression

An expression is executable code that returns a value.

> TODO: Null chaining & coalescing 

## Literal

Represents a [literal](Literals.md) value, namely `null`, booleans, integers,
decimals, strings, atoms, lists, or objects.

```
true
1.0
:atom
{key: "value"}
```

## Group

Represents a parenthesized expression.

```
(expr)
```

## Unary

There are two unary operators currently supported:

 - `-expr`: Numerical negation
 - `!expr`: Logical negation

> TODO: Determine how numerical negation fits into the type system and whether
> it supports operator overloading.

> TODO: Consider alternatives for logical negation to improve readability, such
> as `if (list.!empty())` or `if !(list.empty())`.

> TODO: Consider supporting a coercion operator, `~expr`, that is equivalent to
> `expr.to(ExpectedType)`. This allows `if (~expr)` to effectively be
> `if (expr.isTruthy())`, which also addresses the above point.

## Binary

There are a few categories of binary operators supported:

 - `+`, `-`, `*`, `/`: Mathematical (supports operator overloading)
 - `<`, `<=`, `>`, `>=`: Comparison (operator overloading via `Comprable`)
 - `==`, `!=`: Logical Equality (operator overloading via `Equatable`)
 - `===`, `!==`: Identity Equality
 - `&&`, `||`: Logical And/Or

> TODO: Determine restrictions for `+`/`-`/`*`/`/` with operator overloading,
> such as whether to allow concatenation and set operations.

> TODO: Consider using a function for identity equality instead of dedicated
> operators, such as `Object.identityEquals`.

> TODO: Consider using `and`/`or` instead of `&&`/`||`. 
