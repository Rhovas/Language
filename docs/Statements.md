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
