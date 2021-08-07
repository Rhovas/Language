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

## Is

Checks if an object is the provided type. Though this is a primitive operation,
it uses the syntax of a function call to better support chaining and negation.

```
object is Type
object.method().is(Type)
```

> TODO: Consider supporting `isNotX` syntax sugar for `isX` methods, which would
> also apply to `is` (`object.isNot(Type)`).

## As

Casts an object to the provided type, which throws an exception on failure (thus
having the `!` suffix). Like `is`, `as` uses the syntax of a function call for
the reasons above.

```
object.as!(Type)
```

> TODO: Consider allowing `object.as?(Type)`, which returns `null` if the cast
> fails. This can be special cased, but is better supported as a general
> operation on `Result`s that return `null` on an error.

## Access

Represents accessing a variable (or property when there is a receiver) that can
also be assigned to.

```
variable
object.property = 
```

## Index

Represents indexing a contained variable that can also be assigned to. Indexing
supports multiple arguments and be defined by overloading `[]` and `[]=`.

```
list[0]
grid[x, y] = 0;
```

> TODO: Consider providing a default implementation for multiple arguments that
> allows `grid[x, y]` to be used when `grid[x]` would return an object that
> support indexing. Note that is not just syntax sugar for `grid[x][y]`, as the
> `grid` object must remain in control over the implementation should it change.

## Function

Represents invoking a function (or method when there is a receiver). Function
names ending with `!` identify functions that can throw exceptions.

```
function()
object.method!(arg)
```

> TODO: Consider allowing function invocation on any expression. This is useful
> for first-class functions, but requires combining variable and function scope
> and doesn't seem to have an advantage over `function.()`. Method receivers
> also need to be considered with this.

Functions can also be used with [pipelining](Pipelining.md) to allow passing the
receiver as the first argument to the function.

```
object.|function()
object.|Module.function(arg2)
```

## Lambda

[Lambda expressions](Lambdas.md) are anonymous functions that tend to infer type
information from the rest of the program.

```
lambda { ... }
lambda |args*| { ... }
```

> TODO: Resolve syntax TODOs in [Lambdas](Lambdas.md).

## Macro

[Macros](Macros.md) are functions that can transform the AST of arguments. When
used with a trailing lambda, it becomes a syntax macro and fully controls the
syntax within the lambda which can be used to implement embedded DSLs.

```
#regular(args*)
#syntax { ... }
```

> TODO: Resolve TODOs in [Macros](Macros.md).
