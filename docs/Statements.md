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

## Declaration

A declaration statement defines a local variable. Variables are normally
immutable (`val`), but can be declared mutable using `var`. The type of the
variable can be inferred if it is assigned with an initial value.

```
val name = expr;
var name: Type;
```

> TODO: Consider restricting shadowing of local variables. The Language Server
> Protocol can also be used to find the declaration of a variable, but this
> still seems problematic even with that.

Variable declarations can also use destructuring, as outlined in [Pattern
Matching](Pattern%20Matching.md).

```
val [x, y, z] = list;
```

> TODO: Consider an alternative approach that uses a single keyword with a
> mutability permission on the binding, as in `val name`/`val +name`. This
> allows defining both immutable and mutable variables in a destructure, and
> also removes a dedicated keyword. If this direction is taken, using a generic
> `def` keyword may be a good approach.
> 
> No keyword is also possible but presents challenges with grammar ambiguities,
> shadowing, and readability. Context access may help with shadowing/readability
> when working with class variables. The Language Server Protocol may also be
> helpful for addressing readability concerns.

## Assignment

An assignment statement sets the value of an existing variable, which is either
an access expression (local variable / object property) or indexing expression.
The variable must be mutable (`var`) and the value must be assignable to the
type of the variable.

```
name = value;
object.property = value;
object[...] = value;
```

> TODO: Consider allowing pattern matching to be used with variable assignment.
> This is primarily dependent on the grammar, but if it can be supported that
> removes the `val` requirement for variables in `for` loops (and related).

> TODO: Consider alternatives for compound assignment operators, which are not
> currently supported. These generally only work well with primitives and don't
> generalize well, but the idea of transforming the value of a variable and
> reassigning it is common.
> 
> One option is a macro, such as `#set (x.y.z) { Z -> Z }`, using a lambda to
> map the initial value to the final value. However, something like this feels
> better handled at the language level.

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

## For

A `for` loop iterates over an iterable value, performing the same behavior as
`for...each` loops in many languages.

```
for (val elem in iterable) { ... }
```

> TODO: Consider removing `val`, which simplifies syntax but helps to identify
> destructuring. This also related to the use of `val` for `match` argument
> bindings as well as `with`.

> TODO: Consider either adding an `in` operator or using alternative syntax to
> avoid introducing a new operator for one purpose.

> TODO: Consider whether implicit `val` allows `for (iterable)`. This may
> present grammar issues if the `val` keyword is not used for bindings.

## While

A `while` loop repeatedly executes code while a condition is true, as in many
languages.:

```
while (cond) { ... }
```

> TODO: Consider supporting variable bindings as with `if` statements.

> TODO: Consider supporting `do while` loops as well, which can better express
> some algorithms. Though uncommon, there currently isn't a reason to remove it.

## Try

A `try` statement is used to handle exceptions and works the same as in many
languages, supporting both `catch` and `finally` blocks. Multiple `catch` blocks
are supported, and at least one `catch` or `finally` must be defined.

```
try {
    ...
} catch (val e: Exception) {
    ...
} finally {
    ...
}
```

> TODO: Consider restricting the ability to `catch` `Error`s, which result from
> assertions or other critical issues that can compromise the system. This needs
> to be considered along with the ability to recover/restart after `Error`s.

> TODO: Consider restricting control flow in `finally` to prevent returns and
> other exceptions from being thrown (but not `Error`s). Preventing exceptions
> may not be possible for cleanup, but this needs to be managed carefully if
> there is an existing exception being thrown (especially for an `Error`).

> TODO: Consider supporting syntax for silencing exceptions, such as
> `try! { ... }` or a `#silence(...)` macro. This depends on the behavior of
> silencing, how frequently it's needed, and alternative syntax / API design. In
> general, silencing is to get around the compiler which should not be common.

## With

A `with` statement is used for managing the automatic acquisition and release
of resources. Resources are acquired when entering the body and released when
exited, including when an exception is thrown.

```
with (resource) { ... }
with (val name = resource) { ... }
```

> TODO: Consider allowing `with` to be used for non-resource values, thus acting
> as a scoped declaration as in `with (val name = ...) if (name) { ... }`.

> TODO: Examine the effectiveness of `with` compared to `defer/defererr`, which
> are likely more powerful.

## Label

A label statement is used to identify a jump point for `break` and `continue`.
Labels can be only be applied to loops (`for`/`while`).

```
label: stmt;
```

## Break

A `break` statement exits the current loop or, if a label is provided, the loop
at that label.

```
break;
break label;
```

> TODO: Consider requiring a label to be used, either overall or in certain
> situations (such as with nested loops). Including default `for`/`while` labels
> may make this easier as well (preventing their use when nested).

> TODO: Consider whether `break` (and `continue`) are necessary. Many imperative
> algorithms where `break`/`continue` are useful may be better expressed using
> declarative counterparts, especially given `for` only applies to `Iterable`s.
> It may be preferable to avoid these, using `while` where needed and ensuring
> the loop control flow is identified purely by the condition.

## Continue

A `continue` statement proceeds to the next iteration of the current loop or, if
a label is provided, the loop at that label.

```
continue;
continue label;
```

> TODO: Resolve related `break` TODOs.

## Return

A `return` statement finishes the execution of a function and optionally returns
a value.

```
return;
return value;
```

> TODO: Determine how `return` interacts with lambdas, as lambdas which are
> inlined would return to a different location.

> TODO: Consider allowing labels to be used with return, which can be used to
> specify the return point (especially when involving lambdas above).

## Throw

A `throw` statement is used to raise an exception.

```
throw error;
```

> TODO: Resolve related TODOs in [Error Handling](Error%20Handling.md),
> particularly the type hierarchy for what can be thrown.

## Assert

An `assert` statement is used to validate an expected condition about the state
of the program. Assertions for preconditions, postconditions, and invariants
should instead use `require`/`ensure`.

```
assert cond;
assert cond: "message";
```

> TODO: Consider alternative syntax to be more consistent with other statements,
> such as `assert (cond) { message }`. Alternatively, standardize syntax for
> same-like statements like `if (cond): stmt` and `assert (cond): message`.

> TODO: Consider allowing a block to be used, which aids the other points and
> can support more complex situations. For example, it may be beneficial for
> assertions to define variables accessible for future assertions, especially
> when checking mutations (since always copying the original is not optimal).

## Require

A `require` statement works in the same way as `assert` but is specifically for
validating preconditions, which applies to the user.

```
require cond;
require cond: "message";
```

## Ensure

An `ensure` statement works in the same way as `assert` but is specifically for
validating postconditions and invariants, which applies to the implementation.

```
ensure cond;
ensure cond: "message";
```

> TODO: Determine how to access initial variables, such as `this.initial` and
> `this.final` instead of just `this`. Can also apply to parameters.

> TODO: Determine how `ensure` applies to scope, as different control flow may
> bypass some statements. Consider having `ensure` act similarly to `defer` as
> an assertion which is accumulated and then run when the function returns.
