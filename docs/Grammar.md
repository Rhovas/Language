# Grammar

The formal grammars for the lexer/parser, each of which has two types:

 - The 'main' grammar, which identifies the core language of the lexer/parser.
 - The 'decision' grammar, which identifiers implementation rules that specify
   when an alternative 'commits' - more specifically, when lookahead for that
   alternative completes and lexing/parsing begins.
    - This is primarily for specifying error handling behavior.

These grammars are designed to be viewable using the online
[Railroad Diagram Generator](https://www.bottlecaps.de/rr/ui).

## Lexer

```ebnf
source ::= (whitespace | comment | token)*

whitespace ::= "[ \t\n\r]"

comment ::= "/" "/" [^"\n"\r]*

token ::= identifier | integer | decimal | string | operator

identifier ::= [A-Za-z_] [A-Za-z0-9_]*

integer ::=
    [0-9]+ |
    "0" "b" [0-1]+ |
    "0" "o" [0-7]+ |
    "0" "x" [0-9A-F]+

decimal ::= [0-9]+ "." [0-9]+ ("e" [+-]? [0-9]+)?

string ::=
    '"'
    (
        "\" ('[nrt\"\$\\]' | 'u' [0-9A-F] [0-9A-F] [0-9A-F] [0-9A-F]) |
        '[^\n\r\"]'
    )*
    '"'

operator ::= [^A-Za-z0-9_\"]
```

### Decision Grammar

```ebnf
lex_token ::=
    (
        "/" "/" "commit comment" "[^\n\r]"* |
        "[ \t\n\r]"
    )*
    (
        [A-Za-z_] "commit identifier" lex_identifier |
        [0-9] "commit number" lex_number |
        '"' "commit string" lex_string |
        . "commit operator" lex_operator
    )?

lex_identifier ::= [A-Za-z_] [A-Za-z0-9_]*

lex_number ::=
    "0" "b" [0-1] "commit binary" [0-1]* |
    "0" "o" [0-7] "commit octal" [0-7]* |
    "0" "x" [0-9A-F] "commit hexadecimal" [0-9A-F]* |
    [0-9]+ (
        "." [0-9] "commit decimal" [0-9]* (
            "e" [+-]? [0-9] "commit scientific" [0-9]*
        )?
        | "commit integer"
    )

lex_string ::=
    '"'
    (
        "\" "commit escape" (
            'u' "commit unicode" [0-9A-F] [0-9A-F] [0-9A-F] [0-9A-F] |
            '[nrt\"\$\\]'
        ) |
        '[^\n\r\"]'
    )*
    '"'

lex_operator ::= [^A-Za-z0-9_\"]
```
