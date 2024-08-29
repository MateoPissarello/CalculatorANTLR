
# Calculadora ANTLR
Este proyecto realiza las implementaciones necesarias para ejecutar una calculadora utilizando ANTLR v4, con un patron de diseño Visitor.

**¿Que es un patron de diseño Visitor?**

El patrón de diseño Visitor es una técnica en programación orientada a objetos que permite definir nuevas operaciones sin cambiar las clases de los elementos sobre los que opera. Este patrón es útil cuando tienes una estructura de objetos estable y quieres agregar nuevas funcionalidades sin modificar las clases existentes.

**¿Cómo funciona el patrón Visitor?**

El patrón Visitor separa el algoritmo de la estructura de datos. Básicamente, tienes dos partes:

- **Visitable (Elemento)**: Las clases que forman la estructura de datos implementan una interfaz o clase base común que acepta un Visitor.
- **Visitor**: Una interfaz o clase base que declara métodos para operar en los objetos Visitable. Cada clase concreta de Visitor implementa estos métodos para realizar operaciones específicas.

**Uso del patrón Visitor en ANTLR**

**ANTLR** usa el patrón Visitor para recorrer y operar sobre los nodos de un árbol de sintaxis abstracta (AST) generado a partir del análisis de un lenguaje. Cuando defines una gramática en ANTLR, se genera un árbol de nodos, donde cada tipo de nodo corresponde a una regla en la gramática.

- **Visitable**: Los nodos del árbol de sintaxis que implementan métodos accept.
- **Visitor**: ANTLR genera una interfaz o clase base Visitor que tiene métodos para cada tipo de nodo en el AST.

Cuando quieres realizar operaciones en los nodos del árbol, como evaluaciones, transformaciones o análisis, defines una clase que implementa la interfaz Visitor generada por ANTLR y sobreescribes los métodos necesarios para cada tipo de nodo que te interesa procesar.

Este enfoque es útil para mantener el código modular y permite añadir nuevas operaciones sin modificar la estructura del AST.
## Estructura del proyecto
La mayoria de archivos dentro del proyecto son generados por ANTLR v4, sin embargo, para que esto sea posible es necesario contar con los siguientes:
- CommonLexer.g4 (Archivo donde se definen los Tokens)
```java
lexer grammar CommonLexerRules;
ID: [a-zA-Z]+;
INT: [0-9]+;
NEWLINE: '\r'? '\n';
WS: [ \t]+ -> skip;
MUL: '*'; // assigns token name to '*' used above in grammar
DIV: '/';
ADD: '+';
SUB: '-';
```
- LabeledExpr.g4 (Archivo donde se definen las reglas)
```java
grammar LabeledExpr;
import CommonLexerRules;

prog: stat+;

stat:
	expr NEWLINE			# printExpr
	| ID '=' expr NEWLINE	# assign
	| NEWLINE				# blank;

expr:
	expr op = ('*' | '/') expr		# MulDiv
	| expr op = ('+' | '-') expr	# AddSub
	| INT							# int
	| ID							# id
	| '(' expr ')'					# parens;
```
- Calc.java (Archivo que crea el EvalVisitor y es el main del programa)
```java
import org.antlr.v4.runtime.*;
import org.antlr.v4.runtime.tree.*;
import java.io.FileInputStream;
import java.io.InputStream;

public class Calc {
    public static void main(String[] args) throws Exception {
        String inputFile = null;
        if (args.length > 0)
            inputFile = args[0];
        InputStream is = System.in;
        if (inputFile != null)
            is = new FileInputStream(inputFile);
        ANTLRInputStream input = new ANTLRInputStream(is);
        LabeledExprLexer lexer = new LabeledExprLexer(input);
        CommonTokenStream tokens = new CommonTokenStream(lexer);
        LabeledExprParser parser = new LabeledExprParser(tokens);
        ParseTree tree = parser.prog(); // parse; start at prog
        EvalVisitor eval = new EvalVisitor();
        eval.visit(tree);
    }
}
```
- t.expr (Archivo que contiene las operaciones que queremos realizar):
```
193
a = 5
b = 6
a+b*2
(1+2)*3
```
Teniendo estos archivos ahora podemos ejecutar los siguientes comandos:
```bash
antlr4 -no-listener -visitor LabeledExpr.g4
```
```bash
javac Calc.java LabeledExpr*.java
```
```bash
java Calc t.expr
 ```

Al ejecutar el ultimo podemos esperar una salida como la siguiente:
```bash
193
17
9
```


## Autores

- [@MateoPissarello](https://github.com/MateoPissarello) Mateo Pissarello Londoño
- [@DavidRJimenez](https://github.com/DavidRJimenez) David Ricardo Jimenez
- [@CesarMartinez02](https://github.com/CesarMartinez02) Cesar Martinez Andrade

