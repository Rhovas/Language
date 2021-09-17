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

## Parser

```ebnf
statement ::=
    block_statement |
    declaration_statement |
    if_statement |
    match_statement |
    for_statement |
    while_statement |
    try_statement |
    with_statement |
    break_statement |
    continue_statement |
    return_statement |
    throw_statement |
    assert_statement |
    require_statement |
    ensure_statement |
    label_statement |
    expression ('=' expression)? ';'

block_statement ::= '{' statement* '}'

declaration_statement ::= ('val' | 'var') identifier ('=' expression)? ';'

if_statement ::= 'if' '(' expression ')' statement ('else' statement)?
match_statement ::= 'match' ('(' expression ')')? '{'
    (expression ':' statement)*
    ('else' expression? ':' statement)?
    '}'

for_statement ::= 'for' '(' 'val' identifier 'in' expression ')' statement
while_statement ::= 'while' '(' expression ')' statement

try_statement ::= 'try' statement
    ('catch' '(' identifier ')' statement)*
    ('finally' statement)?
with_statement ::= 'with' '(' ('val' identifier '=')? expression ')' statement

label_statement ::= identifier ':' statement
break_statement ::= 'break' identifier? ';'
continue_statement ::= 'continue' identifier? ';'
return_statement ::= 'return' expression? ';'
throw_statement ::= 'throw' expression ';'

assert_statement ::= 'assert' expression (':' expression)? ';'
require_statement ::= 'require' expression (':' expression)? ';'
ensure_statement ::= 'ensure' expression (':' expression)? ';'

expression ::= logical_or_expression

logical_or_expression ::= logical_and_expression ('|' '|' logical_and_expression)*
logical_and_expression ::= equality_expression ('&' '&' equality_expression)*
equality_expression ::= comparison_expression ([!=] '=' '='? comparision_expression)*
comparison_expression ::= additive_expression ([<>] '='? additive_expression)*
additive_expression ::= multiplicative_expression ([+-] multiplicative_expression)*
multiplicative_expression ::= unary_expression ([*/] unary_expression)*

unary_expression ::= [-!] unary_expression | secondary_expression

secondary_expression ::= primary_expression
    (
        '.' access_function_expression |
        '[' (expression (',' expression)* ','?)? ']'
    )*

primary_expression ::=
     'null' |
     'true' |
     'false' |
     integer |
     decimal |
     string |
     ':' identifier |
     '[' (expression (',' expression)* ','?)? ']' |
     '{' (identifier (':' expression)? (',' identifier (':' expression)?)* ','?) '}'
     '(' expression ')' |
     access_function_expression |
     macro_expression

access_function_expression ::=
    identifier
    ('(' (expression (',' expression)* ','?)? ')')?
    (
        ('|' (identifier (',' identifier)* ','?)? '|')?
        '{' statement* '}'
    )?

macro_expression ::= '#' identifier
    (
        '(' (expression (',' expression)* ','?)? ')' ('{' dsl_source '}')? |
        '{' dsl_source '}'
    )
```

### Decision Grammar

```ebnf
statement ::=
    '{' "commit block" block_statement |
    ('var' | 'val') "commit declaration" declaration_statement |
    'if' "commit if" if_statement |
    'match' "commit match" match_statement |
    'for' "commit for" for_statement |
    'while' "commit while" while_statement |
    'try' "commit try" try_statement |
    'with' "commit with" with_statement |
    'break' "commit break" break_statement |
    'continue' "commit continue" continue_statement |
    'return' "commit return" return_statement |
    'throw' "commit throw" throw_statement |
    'assert' "commit assert" assert_statement |
    'require' "commit require" require_statement |
    'ensure' "commit ensure" ensure_statement |
    identifier ':' "commit label" label_statement |
    expression ('=' "commit assignment" expression)? ';'

block_statement ::= '{' statement* '}'

declaration_statement ::= ('val' | 'var') identifier ('=' "commit value" expression)? ';'

if_statement ::= 'if' '(' expression ')' statement ('else' "commit else" statement)?
match_statement ::= 'match' ('(' "commit argument" expression ')')? '{'
    (expression ':' statement)*
    ('else' "commit else" expression? ':' statement)?
    '}'

for_statement ::= 'for' '(' 'val' identifier 'in' expression ')' statement
while_statement ::= 'while' '(' expression ')' statement

try_statement ::= 'try' statement
    ('catch' "commit catch" '(' identifier ')' statement)* 
    ('finally' "commit finally" statement)?
with_statement ::= 'with' '(' ('val' "commit declaration" identifier '=')? expression ')' statement

label_statement ::= identifier ':' statement
break_statement ::= 'break' identifier? ';'
continue_statement ::= 'continue' identifier? ';'
return_statement ::= 'return' expression? ';'
throw_statement ::= 'throw' expression ';'

assert_statement ::= 'assert' expression (':' "commit message" expression)? ';'
require_statement ::= 'require' expression (':' "commit message" expression)? ';'
ensure_statement ::= 'ensure' expression (':' "commit message" expression)? ';'

expression ::= logical_or_expression

logical_or_expression ::= logical_and_expression ('|' '|' "commit logical_or" logical_and_expression)*
logical_and_expression ::= equality_expression ('&' '&' "commit logical_and" equality_expression)*
equality_expression ::= comparison_expression ([!=] '=' '='? "commit equality" comparision_expression)*
comparison_expression ::= additive_expression ([<>] '='? "commit comparison" additive_expression)*
additive_expression ::= multiplicative_expression ([+-] "commit additive" multiplicative_expression)*
multiplicative_expression ::= unary_expression ([*/] "commit multiplicative" unary_expression)*

unary_expression ::= [-!] "commit unary" unary_expression | secondary_expression

secondary_expression ::= primary_expression
    (
        '.' "commit access_function" access_function_expression |
        '[' "commit index" (expression (',' expression)* ','?)? ']'
    )*

primary_expression ::=
     'null' |
     'true' |
     'false' |
     integer |
     decimal |
     string |
     ':' identifier "commit atom" |
     '[' "commit list" (expression (',' expression)* ','?)? ']' |
     '{' "commit object" (identifier (':' "commit value" expression)? (',' identifier (':' "commit value" expression)?)* ','?) '}' |
     '(' "commit group" expression ')' |
     identifier "commit access_function" access_function_expression |
     '#' identifier "commit macro" macro_expression

access_function_expression ::=
    identifier
    ('(' "commit arguments" (expression (',' expression)* ','?)? ')')?
    (
        ('|' "commit parameters" (identifier (',' identifier)* ','?)? '|')?
        '{' "commit lambda" statement* '}'
    )?

macro_expression ::= '#' identifier
    (
        '(' "commit arguments" (expression (',' expression)* ','?)? ')' ('{' "commit dsl" dsl_source '}')? |
        '{' "commit dsl" dsl_source '}'
    )
```
