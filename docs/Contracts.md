# Contracts

As a language intended for API design and enforcement, [Contracts](https://www.eiffel.org/doc/eiffel/ET-_Design_by_Contract_(tm)%2C_Assertions_and_Exceptions)
are an integral part of Rhovas. Contracts act as a formal specification for the
behavior of a function and cover two key areas:

 - Function inputs/outputs, namely arguments and the return value or error.
 - External state, either the method receiver object (`this`) or globally.

## Assertions

In addition to `assert`, Rhovas supports two other assertions specifically
for enforcing parts of contracts that cannot be covered by the type system:

 - `require` is used for preconditions (user)
 - `ensure` is used for postconditions and invariants (implementation)

By default, assertions are always disabled for production builds (and enabled
for all others).

> TODO: Consider supporting caller-dependent assertions, such as only enforcing
> assertions when the caller is from an external module (like using a library).
> This should be looked at from both the implementation and callers perspective
> as well, and should potentially be configurable per library.
> 
> Related to this, enforcing invariants is tricky when calling other methods as
> work to restore the invariant may still be in-progress. A 'hacky' solution is
> to transform external callers to add a `.assertInvariants()` call afterwards,
> which checks assertions and then returns `this`, which does not account for
> exceptions being thrown.
