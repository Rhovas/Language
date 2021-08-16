# Error Handling

Error handling in Rhovas is primarily through exceptions, however errors can be
worked with as [`Result`s](Results.md) when needed. Errors are generally either
recoverable or unrecoverable depending on whether they can reasonably occur as
part of normal execution. A good resource for background information on this is
the [Midori Error Model](http://joeduffyblog.com/2016/02/07/the-error-model),
and Rhovas follows a similar approach.

> TODO: Error silencing.

> TODO: Type hierarchy for errors.

## Exceptions

Functions that can error have names suffixed with `!`, allowing them to be
easily identified in surrounding code. The types of error that the function can
throw are included after the return type following a `?` separator (which can
use `|` for multiple error).

```
func parse!(string): Integer ? ParseError { ... }
```

> TODO: Consider alternative syntax for specifying exceptions, such as `throws`.
> This does not work well with readability symbols are used elsewhere, but there
> may be another solution better than `?` as well.

## Result Transformation

In some cases, it may be desirable to work with result types rather than
exceptions. Dropping the `!` suffix from the function name will instead return
a `Result` object, which can be used as described in [Results](Results.md).

```
val result = Integer.parse!(string);
val integer = result.or { x };
```

> TODO: Consider allowing `name()` to be explicitly defined, allowing the result
> form to avoid the unnecessary throwing of exceptions for performance.
