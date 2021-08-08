# Statements

A statement is executable code that does not return a value, instead performing
computation through side effects by modifying variables, objects, or the control
flow of a program.

> TODO: Consider allowing blocks, `if`, and `match` to be used as expressions.
> 
>  - Blocks would return the result of the last statement, if it is an
>    expression, or `null`.
>  - `if` and `match` would return the union of all branches.

### Semicolons

Rhovas requires semicolons to help identify the end of the statements for better
error handling and recovery. Block statements, however, are the exception.

> TODO: Consider allowing trailing lambdas to be without semicolons, as they are
> effectively blocks. Could restrict this to trailing lambdas returning `Void`.

> TODO: Consider removing semicolons completely. This is dependent on a viable
> alternative to always identify the end of a statement spanning multiple lines,
> such as indentation.

## Block

A block statement is a container for multiple statements and introduces a new
scope.

```
{
    stmt1;
    stmt2;
    stmt3;
}
```

> TODO: Consider restricting blocks to other statements, thus disallowing
> top-level blocks as they only impact scope handing (for which there are likely
> better approaches).

> TODO: Consider allowing indentation-based blocks using `:`, which can work
> with normal `{}` blocks. This works well with existing syntax for `match`
> cases (`pattern: statement`), single-line `if` statements, and messages with
> `assert`/`require`/`ensure` (`assert (expr): message`).

## If

An `if` statement is used for branching based on a condition and works the same
as in many languages. The condition must evaluate to a `Boolean`.

```
if (cond) { ... }
if (cond) { ... } else { ... }
```

> TODO: Consider supporting syntax sugar for negation, such as `if !(cond)`, to
> improve readability with complex conditions.

> TODO: Consider supporting truthy/falsy behavior. This may improve readability
> in some simple cases, such as `if (list)`, but may not be valuable over
> `if (list.isNotEmpty())`. If supported, this is likely best done by having
> the condition evaluate to a value implementing a `Truthy` type.

> TODO: Consider supporting variable bindings for result types, such as
> `if (val success = result) { ... }`. This can be specifically for results or
> generalized to work with `Truthy` above.

## Match

A `match` statement is intended for evaluating multiple related conditions.
There are two types of `match` statements: conditional, which is effectively
`if`/`else` chains, and structural, which pattern matches on an argument.

> TODO: Syntax for multiple cases, which depends on multiple arguments for
> conditional `match` and the syntax of pattern matching for alternatives.

### Conditional Match

A conditional `match` is simply a chain of arbitrary conditions, working in the
same was as `if`/`else` chains. Conditions are not required to be exhaustive,
so the `else` case is optional. The `else` condition acts like an assertion.

```
match {
    cond1: ...
    cond2: ...
    else cond3: ... (optional)
}
```

> TODO: Consider always requiring `else`, using `else: continue` or similar when
> the cases are not intended to be exhaustive (rather than leaving it implicit).

### Structural Match

A structural `match` applies pattern matching on the argument for each case.
Unlike conditional `match`, a structural `match` must be exhaustive. The `else`
case can be used when the compiler is not able to verify the patterns are
exhaustive, which as above acts like an assertion.

```
match (object) {
    pattern1: ...
    pattern2: ...
    else pattern3: ... (optional when patterns are verifiably exhaustive)
}
```

> TODO: Consider supporting variable bindings for the argument, as in
> `match (val object = ...)`. This address the edge case of trying to bind the
> full pattern to a name like `name: pattern:`, which is ambiguous due to `:`.

> TODO: Consider supporting multiple arguments, as in `match (x, y, z)`. This is
> not strictly needed as multiple arguments can be wrapped in a list literal.
