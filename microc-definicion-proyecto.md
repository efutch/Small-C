# µ-C: Manual de referencia del lenguaje Micro-C

## Introducción

Este manual de referencia describe en detalle el lenguaje Micro-C, para ser implementado como proyecto de los cursos de Compiladores I y II en la Universidad Tecnológica Centroamericana (UNITEC), campus Tegucigalpa.

Debe notarse que la definición contiene algunas imprecisiones, ambigüedades o errores que el implementador (alumno) debe resolver.

Preguntas y observaciones pueden ser dirigidas al Prof. Egdares Futch al correo efutch arroba gmail punto com.

## Convenciones léxicas

Micro-C es *case sensitive*, es decir que las letras mayúsculas y minúsculas se tratan como caracteres diferentes.

### Comentarios y caracteres ignorados

*Comentario* son secuencias de caracteres dentro de un par de secuencias `/* ... */` pareados. Los comentarios pueden extenderse sobre varias líneas, pero los comentarios anidados no se reconocen, es decir que el primer `*/` encontrado dentro de un comentario lo cierra. Los comentarios son ignorados.

Otros caracteres ignorados incluyen el newline, la tabulación horizontal, el CR, y el espacio blanco.

## Tokens

Las secuencias de caracteres encerrados dentro de dos apóstrofes (`'`) son símbolos terminales. Cualquier otra secuencia de caracteres denota el nombre de una clase léxica, p.e. `letra` (véase abajo).

En secciones posteriores utilizaremos las definiciones léxicas siguientes:

- `letra = '_' | 'a' | 'b' | ... | 'z' | 'A' | 'B' | ... | 'Z'`
- `dígito = '0' | '1' | ... | '9'`

Observe que el carácter del underscore (`_`) está tratado como una letra.

### Identificadores

*Un identificador* es una secuencia finita de letras y de dígitos que comienza con una letra o un símbolo de subrayado (underscore). La longitud máxima es de 12 caracteres.

- `identificador = (letra | underscore) (letra | dígito)*`

### Constantes numéricas

*Un integer_constant* es una secuencia de dígitos:

- `integer_constant = digit+`

Una constante numérica se debe separar de un identificador o de una palabra clave. Las constantes numéricas deben aceptar únicamente números positivos y negativos de 16 bits representados en complemento a dos.

Adicionalmente, el compilador debe permitir el ingreso de constantes numéricas hexadecimales, en formato normal de C (`0xFB`, `0xAD`, etc.).

### Constantes de carácter

Un `char_constant` se define como una comilla simple de apertura (`'`), un carácter ASCII extendido imprimible y una comilla simple de cierre.

### Strings constantes

*Una constante de string* es una secuencia de los caracteres incluidos dentro de dos comillas dobles (`"`). Una constante de string puede incluir la secuencia `\"` que representa un carácter de comilla doble en la secuencia en la cual ocurre, tal que no termina el string. La secuencia `\n` representa el carácter del NEWLINE, mientras que la secuencia `\\` representa el carácter del backslash y se puede incluir en un string también.

Una secuencia consistente de un backslash seguido por cualquier carácter a excepción de `n`, `\` o `"` es ilegal. Por consiguiente, un string constante no debe extenderse más allá del extremo de la línea. Un par de `/* ... */` dentro de un string constante no se trata como comentario.

### Operadores

- `add_op = '+' | '-'`
- `mul_op = '*' | '/'`
- `eq_op = '==' | '!='`
- `rel_op = '<' | '<=' | '>=' | '>'`

### Otros símbolos

Otros símbolos que aparecen en la gramática abajo se denotan dentro de comillas dobles. Las palabras reservadas están además marcadas en negrita. Los tokens que fueron definidos arriba se imprimen en negrilla.

## Gramática de Micro-C

| Regla | | Producción |
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

## Detalles de implementación

El lenguaje µ-C es un subconjunto pequeño del lenguaje de programación C. Cualquier programa correcto escrito en µ-C debe compilar correctamente con un compilador de ANSI C.

### Tipos de datos

µ-C ofrece dos tipos de datos del estándar (predefinido): **integer** y **char**.

### Bloques

µ-C sigue las reglas usuales de scoping. Las funciones pueden ser recursivas.

Cada identificador de variable o de función se debe declarar antes de su uso.

### Expresiones

Hay cuatro operadores de aritmética binaria: `+`, `-`, `*`, `/`. `+` y `-` tienen precedencia más baja que `*` y `/`. Además, `+` y `-` pueden ser utilizados como operadores unarios.

Con argumentos de tipo entero, cada operación devuelve resultado entero.

Hay seis operadores relacionales binarios: `<`, `<=`, `==`, `!=`, `>=`, `>`. Los tipos se manejan como arriba. Los operadores de igualdad (`==` y `!=`) tienen precedencia más baja que los otros operadores relacionales.

Las expresiones de asignación se hacen por medio del operador de asignación (`=`) que es de una precedencia más baja que los operadores relacionales.

### Statements

µ-C tiene cinco diferentes statements: expresión, retorno, `while`, `if`, y bloque. La semántica de cada statement es igual que en C. Todos los parámetros son pasados a las funciones por valor.

### Biblioteca de runtime para I/O

µ-C proporciona versiones simples de dos funciones de I/O de la biblioteca estándar de C: **printf** y **scanf**.

**scanf** requiere un parámetro, el cual es una secuencia de formato (una constante de string) que puede ser `"%d"` o `"%c"` solamente. La función retorna el entero o char leído del stdin.

**printf** acepta uno o dos parámetros. Esta función se utiliza para escribir el valor de una expresión (entera o char) o de una constante de string. El primer parámetro es una secuencia de formato (una constante de string) que puede contener una sola conversión `"%d"` o `"%c"` (para imprimir un `%` se escribe `%%`). El segundo parámetro (si existe) se imprime de acuerdo a lo especificado por el directivo de conversión especificado en el primer parámetro.

## Ejemplos

### Cálculo de una expresión simple

```c
/*
 * Programa de prueba #1
 */
int main( void )
{
    int i, j;

    i = scanf("%d");                  /* lea i */
    j = 9 + i * 8;                    /* evalue j */
    printf( "Resultado es %d\n", j ); /* imprima j */
}
```

### Llamada simple a función

```c
/*
 * Programa de prueba #2
 */
int count( int n );

int
main( void )
{
    int i, sum;

    i = scanf( "%d", &i );   /* lea i */
    sum = count(i);          /* llame a count */
    printf( "%d\n", sum );   /* imprima resultados */
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

### Una función recursiva simple

```c
/*
 * Cálculo recursivo del factorial
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

    printf( "Ingrese un entero: " );
    n = scanf( "%d" );                /* lea i */
    fact = factorial( n );            /* llame a factorial */
    printf( "Factorial de %d ", n );
    printf( "es %d\n", fact );
}
```

## Adiciones a incluir

Para diferenciar los proyectos, se agregará una característica adicional que cada alumno deberá incluir en la especificación base del lenguaje. Para saber qué característica le corresponde, deberá obtener el módulo 3 de su número de cuenta, de acuerdo a la siguiente tabla:

| Módulo 3 del número de cuenta del estudiante | Característica a incluir |
|---|---|
| 0 | Uso de arreglos, incluyendo declaración y uso. |
| 1 | Incorporación del statement `repeat-until`. |
| 2 | Uso de structs, incluyendo declaración y uso de miembros. |

## Puntos de evaluación

La evaluación del proyecto se basa en la detección de errores y la generación de código correcto. Para reportar los errores encontrados en el código fuente, se deberá imprimir al menos el número de línea y columna así como una descripción útil del error encontrado. El compilador deberá detenerse al encontrar el primer error.

Los puntos que serán evaluados para obtener la calificación del proyecto son los siguientes:

- Implantación de la librería de runtime en assembler.
- Detección de errores léxicos, incluyendo los referentes a comentarios, rango de enteros y strings.
- Ningún identificador deberá ser declarado dos veces en el mismo ámbito.
- Ningún identificador podrá ser usado sin haberse declarado previamente.
- El programa fuente deberá tener un procedimiento llamado main, sin parámetros.
- Chequeo de tipos en expresiones, de acuerdo a lo establecido en este manual.
- El número y tipos de los argumentos en las llamadas a funciones y procedimientos deberá ser igual a los declarados.
- Un statement return no debe retornar un valor a menos que se encuentre dentro de una función que haya sido declarada que retorna un valor.
- La expresión en un return debe tener el mismo tipo de la función donde esté contenida.
- Los statements *break* y *continue* solo pueden aparecer dentro de bloques contenidos en statements *while* (o en *repeat-until*).
- Generación de código intermedio por medio de AST.
- Generación de código MIPS.
- Opcional para extra crédito: al menos dos optimizaciones simples.
- Opcional para extra crédito: IDE que permita cargar archivos fuente y compilarlos directamente.
