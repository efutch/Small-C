# µ-C: Micro-C Language Reference Manual

## Introduction

This reference manual describes the Micro-C language in detail as the project definition for the Compilers I and II courses at the Universidad Tecnológica Centroamericana (UNITEC) in Tegucigalpa.

Note that the definition contains some inaccuracies, ambiguities, or errors that the implementer (student) must resolve.


## Lexical Conventions

Micro-C is *case sensitive*; that is, uppercase and lowercase letters are treated as different characters.

### Comments and Ignored Characters

A *comment* is a sequence of characters within a matching pair of `/* ... */` sequences. Comments may span multiple lines, but nested comments are not recognized; that is, the first `*/` found inside a comment closes it. Comments are ignored.

Other ignored characters include newline, horizontal tab, CR, and whitespace.

## Tokens

Sequences of characters enclosed within two apostrophes (`'`) are terminal symbols. Any other sequence of characters denotes the name of a lexical class, e.g. `letter` (see below).

In later sections we will use the following lexical definitions:

- `letter = '_' | 'a' | 'b' | ... | 'z' | 'A' | 'B' | ... | 'Z'`
- `digit = '0' | '1' | ... | '9'`

Note that the underscore character (`_`) is treated as a letter.

### Identifiers

*An identifier* is a finite sequence of letters and digits that begins with a letter or an underscore. The maximum length is 12 characters.

- `identifier = (letter | underscore) (letter | digit)*`

### Numeric Constants

*An integer_constant* is a sequence of digits:

- `integer_constant = digit+`

A numeric constant must be separated from an identifier or a keyword. Numeric constants must accept only positive and negative 16-bit numbers represented in two's complement.

Additionally, the compiler must allow hexadecimal numeric constants in standard C format (`0xFB`, `0xAD`, etc.).

### Character Constants

A `char_constant` is defined as an opening single quote (`'`), one printable extended ASCII character, and a closing single quote.

### String Constants

*A string constant* is a sequence of characters enclosed within two double quotes (`"`). A string constant may include the sequence `\"`, which represents a double-quote character within the string in which it occurs, so that it does not terminate the string. The sequence `\n` represents the NEWLINE character, while the sequence `\\` represents the backslash character and may also be included in a string.

A sequence consisting of a backslash followed by any character other than `n`, `\`, or `"` is illegal. Consequently, a string constant must not extend past the end of the line. A `/* ... */` pair inside a string constant is not treated as a comment.

### Operators

- `add_op = '+' | '-'`
- `mul_op = '*' | '/'`
- `eq_op = '==' | '!='`
- `rel_op = '<' | '<=' | '>=' | '>'`

### Other Symbols

Other symbols appearing in the grammar below are denoted within double quotes. Reserved words are additionally marked in bold. Tokens that were defined above are printed in bold.

## Micro-C Grammar

| Rule | | Production |
|---|---|---|
| *translation_unit* | → | *external-declaration* \| |
| | | *translation_unit external-declaration* |
| *external-declaration* | → | *function-definition* \| |
| | | *declaration* |
| *function_definition* | → | *function_def_header function_body* |
| *function_def_header* | → | *return_type* **identifier** **"("** *parameters_def* **")"** |
| *return_type* | → | *type* \| **"void"** |
| *parameters_def* | → | *parameter_def_list* \| **"void"** |
| *parameter_def_list* | → | *type* **identifier** \| |
| | | *parameter_def_list* **","** *type* **identifier** |
| *function_body* | → | **"{"** *declarations statement_list* **"}"** |
| *declarations* | → | *declarations declaration* \| λ |
| *declaration* | → | *variable_declaration* \| |
| | | *function_declaration* |
| *variable_declaration* | → | *type identifier_list* **";"** |
| *function_declaration* | → | *return_type* **identifier** **"("** *parameters_decl* **")"** **";"** |
| *parameters_decl* | → | *parameter_decl_list* \| *parameter_decl_list* **","** **"..."** \| **"void"** |
| *parameter_decl_list* | → | *parameter_decl_spec* \| |
| | | *parameter_decl_list* **","** *parameter_decl_spec* |
| *parameter_decl_spec* | → | *type* **identifier** \| **"char"** **"\*"** **identifier** |
| *statement_list* | → | *statement_list statement* \| *statement* |
| *statement* | → | *expression* **";"** \| |
| | | **"return"** **";"** \| |
| | | **"return"** *expression* **";"** \| |
| | | **"while"** **"("** *expression* **")"** *statement* \| |
| | | **"if"** **"("** *expression* **")"** *statement* \| |
| | | **"if"** **"("** *expression* **")"** *statement* **"else"** *statement* \| |
| | | **"{"** *statement_list* **"}"** \| |
| | | **"break"** **";"** \| |
| | | **"continue"** **";"** |
| *expression* | → | *equality_expression* **"="** *equality_expression* \| |
| | | *equality_expression* |
| *equality_expression* | → | *relational_expression* **eq_op** *relational_expression* \| |
| | | *relational_expression* |
| *relational_expression* | → | *simple_expression* **rel_op** *simple_expression* \| |
| | | *simple_expression* |
| *simple_expression* | → | *simple_expression* **add_op** *term* \| |
| | | *term* |
| *term* | → | *term* **mul_op** *factor* \| *factor* |
| *factor* | → | *constant* \| **identifier** \| **"("** *expression* **")"** \| |
| | | **add_op** *factor* \| **identifier** **"("** *expression_list* **")"** \| **identifier** **"("** **")"** |
| *constant* | → | **string_constant** \| *numeric_constant* \| *char_constant* |
| *numeric_constant* | → | **integer_constant** |
| *expression_list* | → | *expression* \| *expression_list* **","** *expression* |
| *identifier_list* | → | **identifier** \| *identifier_list* **","** **identifier** |
| *type* | → | **"int"** \| **"char"** |

## Implementation Details

The µ-C language is a small subset of the C programming language. Any correct program written in µ-C must compile correctly with an ANSI C compiler.

### Data Types

µ-C offers two standard (predefined) data types: **integer** and **char**.

### Blocks

µ-C follows the usual scoping rules. Functions may be recursive.

Every variable or function identifier must be declared before use.

### Expressions

There are four binary arithmetic operators: `+`, `-`, `*`, `/`. `+` and `-` have lower precedence than `*` and `/`. In addition, `+` and `-` may be used as unary operators.

With integer arguments, each operation returns an integer result.

There are six binary relational operators: `<`, `<=`, `==`, `!=`, `>=`, `>`. Types are handled as above. The equality operators (`==` and `!=`) have lower precedence than the other relational operators.

Assignment expressions are made with the assignment operator (`=`), which has lower precedence than the relational operators.

### Statements

µ-C has five different statements: expression, return, `while`, `if`, and block. The semantics of each statement are the same as in C. All parameters are passed to functions by value.

### Runtime Library for I/O

µ-C provides simple versions of two I/O functions from the standard C library: **printf** and **scanf**.

**scanf** takes one parameter, which is a format string (a string constant) that may be only `"%d"` or `"%c"`. The function returns the integer or char read from stdin.

**printf** accepts one or two parameters. This function is used to write the value of an expression (integer or char) or of a string constant. The first parameter is a format string (a string constant) that may contain a single conversion, `"%d"` or `"%c"` (to print a `%`, write `%%`). The second parameter (if present) is printed according to the conversion directive specified in the first parameter.

## Examples

### Evaluating a Simple Expression

```c
/*
 * Test program #1
 */
int main( void )
{
    int i, j;

    i = scanf("%d");                 /* read i */
    j = 9 + i * 8;                   /* evaluate j */
    printf( "Result is %d\n", j );   /* print j */
}
```

### Simple Function Call

```c
/*
 * Test program #2
 */
int count( int n );

int
main( void )
{
    int i, sum;

    i = scanf( "%d", &i );   /* read i */
    sum = count(i);          /* call count */
    printf( "%d\n", sum );   /* print results */
}

int
count( int n )
{
    int i, sum;

    i = 1;
    sum = 0;
    while ( i <= n ) {
        sum = sum + i;
        i = i + 1;
    }
    return sum;
}
```

### A Simple Recursive Function

```c
/*
 * Recursive factorial computation
 */
int factorial( int n )
{
    if ( n <= 1 )
        return 1;
    else
        return n * factorial( n - 1 );
}

int main( void )
{
    int n, fact;

    printf( "Enter an integer: " );
    n = scanf( "%d" );                /* read i */
    fact = factorial( n );            /* call factorial */
    printf( "Factorial of %d ", n );
    printf( "is %d\n", fact );
}
```

## Additional Features to Include

To improve their grade, students can add one or more additional feature on top of the base language specification from the following table:

| Option | Feature to include |
|---|---|
| 0 | Arrays, including declaration and use. |
| 1 | The `repeat-until` statement. |
| 2 | Structs, including declaration and use of members. |

## Evaluation Criteria

Project evaluation is based on error detection and correct code generation. To report errors found in the source code, the compiler must print at least the line and column number along with a useful description of the error. The compiler must stop at the first error found.

The items that will be evaluated to determine the project grade are the following:

- Implementation of the runtime library in assembly.
- Detection of lexical errors, including those related to comments, integer range, and strings.
- No identifier may be declared twice in the same scope.
- No identifier may be used without having been previously declared.
- The source program must have a procedure named main, with no parameters.
- Type checking in expressions, as established in this manual.
- The number and types of arguments in function and procedure calls must match those declared.
- A return statement must not return a value unless it is inside a function declared to return a value.
- The expression in a return must have the same type as the function containing it.
- The *break* and *continue* statements may only appear inside blocks contained in *while* statements (or in *repeat-until*).
- Intermediate code generation by means of an AST, quads, triples or LLVM.
- Object code generation (MIPS,x86, etc.).
- Optional for extra extra credit: at least two simple optimizations.
- Optional for extra extra credit: an IDE that allows loading source files and compiling them directly.
