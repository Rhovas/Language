# Method Forms

Method forms, for lack of a better name, allow statements to be used with the
syntax of a method instead. These are based on Kotlin's
[Scope Functions](https://kotlinlang.org/docs/scope-functions.html), however do
not make any scope changes with `this`.

 - `.do { ... }`: Executes a lambda taking the receiver as an argument and
   returns the result (Kotlin `let`).
    - `..do { ... }`: Returns the receiver via cascading (Kotlin `also`).
 - `.if { ... }`: Executes a lambda taking the receiver as an argument and
   returns the receiver if the result is true, else `null` (Kotlin `takeIf`).
 - `.match { ... }`: Performs a structural match on the receiver. Note that this
   requires language support for the syntax change to patterns.
 - `.for { ... }`: Iterates over the receiver.

An example using `.if` and `.do` is shown below.

```
string.if { val.size != 0 }?.do {
    ...
}
```
