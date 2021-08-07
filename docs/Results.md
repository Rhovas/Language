# Results

The `Result` type is used for operations which have a failure state, such as
exceptions or nullable types. Rhovas provides a combination of properties,
functions, and syntax sugar (chaining) to work with result types effectively.

Given a result `Result<T, E>`, the following properties and functions exist:

 - `val value: T?`
    - Returns the success value or `null` if the result is an error.
 - `val error: E?`
    - Returns the error or `null` if the result is successful.
 - `func map(f: Func<T, R>): Result<R, E>`
    - Maps the success value by applying the function to the success value,
      maintaining the original error if present.
    - Note that if `R` is itself a `Result<T1, E1>`, the return type is
      `Result<Result<T1, E1>, E>` and not `Result<T1, E | E1>`. For this
      behavior, chaining should be used instead.
 - `func or(t: T): T`/`func or(f: Func<T>): T`
    - Returns the success value or either `t`/`f()` if the result is an error.

> TODO: Consider allowing `or` to take `E` as an argument. This doesn't seem
> like good practice, however there currently isn't a reason to prevent it.

> TODO: Consider supporting the ability to matching directly on the success or
> error value in patterns, effectively treating the result as a union type. This
> is not fully type safe, but would simplify many use cases. It may be possible
> to do this by having some type of `.flatten` method which returns either
> `T | E` (one level) or `T | E0 | E1 | ...` (all levels).

## Chaining

Null chaining is a common language feature for working with `Nullable` types. In
Rhovas, chaining can be used on any `Result` type to apply a field access or
function call to the success value.

```
result?.field;
result?.method();
```

Given a result `Result<T0, E0>`, the return type of this expression depends on
the return type of `.field`/`.method()`.

 - If `.field`/`.method()` returns a `Result<T1, E1>`, the return type is
   `Result<T, E0 | E1>` (combining via union).
 - If `.field`/`.method()` returns a non-result `T1`, the return type is
   `Result<T1, E0>`.

This merging behavior is consistent with null chaining in other languages. If
the result type should not be merged, `.map` should be used instead.
