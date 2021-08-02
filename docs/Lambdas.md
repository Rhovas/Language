# Lambdas

Lambda expressions are anonymous functions that tend to infer type information
from the rest of the program. The base syntax of a lambda is as follows:

```
numbers.map(lambda |x| { x * x })
object.each(lambda |key, value| { print(key + ": " + value) })
```

However, there are two features that simplify common uses of lambdas:

 - The **Implicit Parameter** `val`, which is always accessible and is either
   the value of the lambda's argument if there is only one or is an object
   containing all arguments by name if there are multiple.
    - `numbers.map(lambda { val * val })`
    - `object.each(lambda { print(val.key + ": " + val.value) }`
 - **Trailing Lambdas**, which are passed as the last argument to a function.
   Notably, `lambda` itself is just a function that uses a trailing lambda.
    - `numbers.map |x| { x * x }`
    - `object.each |key, value| { print(key + ": " + value) }`
    - Other arguments can still be passed in as normal, such as:
       - `numbers.reduce(0) |acc, value| { acc + value }`

> TODO: Consider restricting trailing lambdas to when the parameter is itself a
> `Function`, as opposed to an arbitrary `Object`.

Combined, these lambdas can be written as:

```
numbers.map { val * val }
object.each { print(val.key + ": " + val.value) }
```

> TODO: Consider alternative syntax with `func(args*) { ... }` for anonymous
> functions and `.map func(args*) { ... }` for trailing lambdas. This avoids
> adding new syntax for arguments and is more consistent, but longer.

## Pattern Matching

Lambda arguments may also use [pattern matching](Pattern Matching.md), which
acts as an assertion should the match fail.

```
points.map |{x, _, z}| { x * x + z * z }
```
