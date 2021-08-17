# Atom Options

When calling functions with literal values, Rhovas allows atoms to be used
instead for certain types of parameters to improve readability.

> TODO: Consider adding a custom type for defining options using atoms, such as
> `Options<:a, :b, :c>`. This can be used for passing arguments through varargs,
> such as `function(:a, :c)`, and has better support at runtime. However, this
> may introduce complications when normal parameters that are atoms.

## Boolean Parameters

Instead of writing a literal `true`/`false`, a `Boolean` parameter can be set to
`true` by passing an atom with the name of the parameter. Unary negation can be
used to set the value to `false`.

```
function(:option)  //function(option: true)
function(!:option) //function(option: false)
```

> TODO: Determine guidelines for API design with this, such as having `false`
> always be the default such that `!:option` does not have to be used.

## Enum Parameters

Enum parameters don't have the readability issue booleans do, however atoms
simply usage by only requiring the name of the enum constant.

```
function(:CONSTANT)
```
