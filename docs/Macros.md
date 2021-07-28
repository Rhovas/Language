# Macros

Rhovas contains two types of macros:

 - **[Regular Macros](#regular-macros)** work like macros in most languages,
   transforming an existing AST into a new one returned by the macro.
 - **[Syntax Macros](#syntax-macros)** are instead applied during parsing,
   before the AST is created, and can change the syntax of the language.

All macros are prefixed with `#`, allowing macros to be easily identified. There
are a few reasons why it is important to separate macros from functions:

 - Macros are applied at compile time rather than at runtime.
 - Macros can modify the control flow of the program by executing arguments out
   of order, with a delay, conditionally, or repeatedly.
 - Syntax macros in particular can modify the syntax of the language, thus it is
   helpful to clearly identify locations in the code where the syntax changes.

> TODO: Decide whether there should be syntactic differences between regular and
> syntax macros. Though ideal, it's normally clear from context and the only
> conflict arises from regular macros with a trailing lambda.

## Regular Macros

Regular macros transform an existing AST into a new one, operating exactly like
a function that takes AST arguments and returns an AST.

Most of the interesting aspects of regular macros are with defining them, and
there has not been significant work put into this yet. A few TODO notes:

> TODO: Determine the type system for AST values and how it interacts with macro
> arguments. This needs to account for types like `Expression<Boolean>` versus
> `Literal<Boolean>`, especially when working with literal lists/objects.

> TODO: Determine how macros interact with scope, especially for hygiene. This
> may not be easy/possible when transpiling, or may require an anonymous
> function to handle properly.

> TODO: Determine how templating works with macros for inserting and/or
> evaluating arguments (one promising option is using a `#quote` DSL).

> TODO: Consider supporting splicing macros, which allow returning multiple ASTs
> that is 'spliced' into the containing list (such as varargs).

## Syntax Macros

Syntax macros are applied during parsing and can change the syntax of the
language within the trailing lambda block. This allows Rhovas to support
embedded DSLs without any restrictions on the syntax of the DSL.

 - For more information, see the [full thesis](https://ufdc.ufl.edu/AA00082326/00001/citation)
   introducing syntax macros.

Some example applications of syntax macros for embedded DSLs are:

 - Regex: `#regex { /'.*?'/ }`, which matches basic single-quoted strings. The
   use of single-quotes may conflict with lexing for character literals, so it
   is important that syntax macros use a distinct lexer.
 - SQL: `#sql { SELECT * FROM ${table} }`, which uses interpolation to insert a
   table name. This avoids SQL injection since `${table}` is a known variable to
   be inserted and is not string concatenated into the query itself.

In addition to the inline forms above, multiline syntax macros can also be used:

```
#sql {
    SELECT * FROM users
    WHERE name = ${name}
}
```

> TODO: Consider requiring indentation on multiline forms. This would help with
> error recovery and could improve syntax highlighting for unknown DSLs.

Finally, as has been shown interpolation can be used to insert values. Within
interpolation, the syntax changes back to the host language to access variables
with otherwise invalid identifiers (shown below) or perform computation.

```
val snake_case = 0;
#kebab-case-dsl {
    val kebab-case = ${snake_case};
}
```

> TODO: Resolve [Interpolation](Interpolation.md) TODOs for syntax.
