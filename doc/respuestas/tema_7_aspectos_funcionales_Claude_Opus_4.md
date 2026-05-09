# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función?

### Respuesta

Un **puntero a función** es una variable que almacena la dirección de memoria de una función, permitiendo invocarla de forma indirecta. En C, se declara especificando el tipo de retorno y los parámetros de la función a la que apuntará.

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>

char* convertirAMayusculas(char* cadena) {
    for (int i = 0; cadena[i]; i++) {
        cadena[i] = toupper(cadena[i]);
    }
    return cadena;
}

int main() {
    // Puntero a función que recibe char* y devuelve char*
    char* (*aMayusculas)(char*) = convertirAMayusculas;

    char texto[] = "hola mundo";
    printf("%s\n", aMayusculas(texto)); // HOLA MUNDO
    return 0;
}
```

El puntero `aMayusculas` almacena la dirección de `convertirAMayusculas` y se invoca con la misma sintaxis que una llamada normal a función.

---

## 2. ¿Qué es una **función lambda**?

### Respuesta

Una **función lambda** (o función anónima) es una función que se define sin nombre, directamente en el lugar donde se necesita. Permite crear funciones de forma concisa sin tener que declararlas previamente.

En **JavaScript**:

```javascript
const aMayusculas = (cadena) => cadena.toUpperCase();
console.log(aMayusculas("hola mundo")); // HOLA MUNDO
```

En **Java** (usando `Function<String, String>`):

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();
        System.out.println(aMayusculas.apply("hola mundo")); // HOLA MUNDO
    }
}
```

En ambos casos, la función se define en línea y se asigna a una variable local. La diferencia con C es que no se necesita declarar previamente una función con nombre; la lógica se escribe directamente como una expresión.

---

## 3. ¿Qué es el **paradigma funcional**?

### Respuesta

El **paradigma funcional** es un estilo de programación donde las funciones son el elemento central de la computación. Se basa en la idea matemática de función: recibe entradas, produce salidas y, en su forma pura, no tiene efectos secundarios ni modifica estado externo. Lenguajes como Haskell son puramente funcionales; otros como Lisp o Scala combinan paradigmas.

A Java 8 se le llama **multi-paradigma** porque, además de ser orientado a objetos, incorporó elementos funcionales como las expresiones lambda, la interfaz `Stream` y las interfaces funcionales. Esto permite escribir código en estilo funcional cuando resulta más expresivo, sin abandonar la orientación a objetos.

Que las funciones sean **"ciudadanos de primera clase"** significa que pueden tratarse como cualquier otro valor del lenguaje: asignarse a variables, pasarse como argumentos a otros métodos, devolverse como resultado de un método y almacenarse en estructuras de datos. En C las funciones no lo son realmente (solo se pueden usar punteros a ellas); en Java 8+ y JavaScript sí lo son.

---

## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La sintaxis de una lambda en Java tiene la forma `(parámetros) -> cuerpo`. Los paréntesis contienen los parámetros (cuyos tipos pueden omitirse si el compilador los infiere), la flecha `->` separa los parámetros del cuerpo, y el cuerpo puede ser una expresión simple o un bloque entre llaves:

```java
// Lambda con un parámetro (paréntesis opcionales) y expresión simple
x -> x * 2

// Lambda con varios parámetros y expresión simple
(a, b) -> a + b

// Lambda con tipos explícitos
(String s) -> s.toUpperCase()

// Lambda con bloque de código (necesita return explícito si devuelve valor)
(int a, int b) -> {
    int suma = a + b;
    return suma;
}

// Lambda sin parámetros
() -> System.out.println("Hola")
```

Si el cuerpo es una sola expresión, el valor de esa expresión se devuelve automáticamente (sin `return`). Si se usan llaves, es necesario incluir `return` explícitamente cuando la lambda deba devolver un valor. El tipo de la lambda no se declara directamente: Java lo infiere del contexto (la interfaz funcional esperada).

---

## 5. Método `transformar` que recibe una función como parámetro.

### Respuesta

En **Java**:

```java
import java.util.function.Function;

public class Main {
    public static String transformar(String texto, Function<String, String> funcion) {
        return funcion.apply(texto);
    }

    public static void main(String[] args) {
        Function<String, String> aMayusculas = s -> s.toUpperCase();
        String resultado = transformar("hola mundo", aMayusculas);
        System.out.println(resultado); // HOLA MUNDO
    }
}
```

En **JavaScript**:

```javascript
function transformar(texto, funcion) {
    return funcion(texto);
}

const aMayusculas = (s) => s.toUpperCase();
console.log(transformar("hola mundo", aMayusculas)); // HOLA MUNDO
```

En ambos casos, el método `transformar` recibe un `String` y una función que transforma cadenas. Dentro del método, se invoca la función recibida sobre el texto. Esto demuestra el concepto de **funciones de orden superior**: funciones que reciben otras funciones como parámetro.

---

## 6. Invoca `transformar` con una función lambda directamente en la llamada.

### Respuesta

Se puede definir la función lambda directamente en el argumento de la llamada, sin necesidad de asignarla previamente a una variable:

```java
// Lambda que invierte la cadena, definida directamente en la llamada
String invertida = transformar("hola mundo", s -> new StringBuilder(s).reverse().toString());
System.out.println(invertida); // odnum aloh
```

En JavaScript:

```javascript
const invertida = transformar("hola mundo", s => s.split("").reverse().join(""));
console.log(invertida); // odnum aloh
```

Este patrón es muy habitual en programación funcional: se define la lógica exactamente donde se necesita, sin contaminar el espacio de nombres con funciones auxiliares que solo se usan una vez. La expresividad del código aumenta significativamente.

---

## 7. ¿Qué se entiende por cierre o "closure"?

### Respuesta

Un **closure** (cierre) es la capacidad de una función lambda de **capturar y recordar variables del ámbito donde fue definida**, incluso después de que ese ámbito haya terminado. La lambda no solo contiene su código, sino también una referencia al entorno léxico que la rodea.

```java
public static void main(String[] args) {
    String sufijo = " [procesado]"; // Variable local fuera de la lambda

    Function<String, String> concatenador = s -> s + sufijo; // Captura 'sufijo'

    String resultado = transformar("hola", concatenador);
    System.out.println(resultado); // hola [procesado]
}
```

La lambda `concatenador` accede a la variable `sufijo` que está definida fuera de ella. En Java, para que esto funcione, la variable capturada debe ser **efectivamente final** (su valor no puede cambiar después de la inicialización). Esto es una restricción de Java; en otros lenguajes como JavaScript, las closures pueden capturar variables mutables sin restricciones.

---

## 8. ¿En qué se diferencia una función lambda de los punteros a funciones de C?

### Respuesta

La diferencia fundamental es el **closure**. Un puntero a función en C almacena únicamente la dirección de memoria de una función: es una referencia "desnuda" al código ejecutable, sin ningún contexto asociado. Si la función necesita datos externos, estos deben pasarse explícitamente como parámetros adicionales (a menudo mediante un `void*` genérico).

Una función lambda, en cambio, empaqueta **código + contexto** en una misma entidad. Gracias al closure, la lambda captura automáticamente las variables del ámbito donde fue definida y las lleva consigo, sin necesidad de pasarlas como parámetros. Esto permite crear funciones "personalizadas" en el momento, algo que con punteros a funciones en C requeriría soluciones mucho más verbosas y propensas a errores.

Además, las lambdas son **anónimas** (se definen sin nombre, en línea), mientras que un puntero a función en C siempre apunta a una función previamente declarada con nombre. Esta combinación de anonimato y closures hace que las lambdas sean mucho más expresivas y flexibles.

---

## 9. Función que crea funciones "descuento".

### Respuesta

Se crea una función `crearDescuento` que recibe un porcentaje y devuelve una nueva función que aplica ese descuento a cualquier cantidad:

```java
import java.util.function.Function;

public class Main {
    public static Function<Double, Double> crearDescuento(double porcentaje) {
        return precio -> precio * (1 - porcentaje / 100.0);
    }

    public static void main(String[] args) {
        Function<Double, Double> descuento10 = crearDescuento(10);
        Function<Double, Double> descuento25 = crearDescuento(25);

        System.out.println(descuento10.apply(100.0)); // 90.0
        System.out.println(descuento25.apply(200.0)); // 150.0
    }
}
```

La **closure** en este ejemplo es especialmente interesante: cada lambda devuelta por `crearDescuento` captura el valor de `porcentaje` que recibió como argumento. Aunque el método `crearDescuento` ya ha terminado su ejecución, las lambdas `descuento10` y `descuento25` siguen recordando sus respectivos porcentajes (10 y 25). Cada función "recuerda" su propio contexto de creación, lo que permite generar funciones especializadas de forma dinámica.

---

## 10. ¿Qué es una **interfaz funcional**?

### Respuesta

Una **interfaz funcional** es una interfaz que tiene exactamente **un único método abstracto** (SAM: *Single Abstract Method*). En Java, toda expresión lambda debe tener un tipo, y ese tipo es siempre una interfaz funcional. La lambda se convierte en la implementación de ese único método abstracto.

Los requisitos son: (i) ser una interfaz, (ii) tener exactamente un método abstracto (puede tener métodos `default` o `static` adicionales), y (iii) opcionalmente estar anotada con `@FunctionalInterface` para que el compilador verifique que cumple el requisito. Ejemplos: `Runnable` (método `run()`), `Comparator<T>` (método `compare()`), `Function<T,R>` (método `apply()`).

```java
@FunctionalInterface
interface Operacion {
    double calcular(double a, double b);
}

// La lambda implementa el método 'calcular'
Operacion suma = (a, b) -> a + b;
System.out.println(suma.calcular(3, 4)); // 7.0
```

---

## 11. Interfaz funcional `Transformador`.

### Respuesta

Se define una interfaz funcional que transforma un `String` en otro `String`:

```java
@FunctionalInterface
interface Transformador {
    String transformar(String entrada);
}
```

Ahora se puede usar esta interfaz como tipo para lambdas que transforman cadenas:

```java
Transformador aMayusculas = s -> s.toUpperCase();
Transformador invertir = s -> new StringBuilder(s).reverse().toString();

System.out.println(aMayusculas.transformar("hola")); // HOLA
System.out.println(invertir.transformar("hola"));     // aloh
```

El método `transformar` definido anteriormente podría reescribirse usando `Transformador` en lugar de `Function<String, String>`, con la ventaja de que el nombre de la interfaz y su método son más descriptivos para el dominio.

---

## 12. `Transformador` genérico.

### Respuesta

Se generaliza `Transformador` con parámetros de tipo para convertir de un tipo `T` a un tipo `R`:

```java
@FunctionalInterface
interface Transformador<T, R> {
    R transformar(T entrada);
}
```

Ejemplo de un transformador que redondea un `Double` en un `Integer`:

```java
Transformador<Double, Integer> redondear = d -> (int) Math.round(d);

System.out.println(redondear.transformar(3.7));  // 4
System.out.println(redondear.transformar(2.3));  // 2
```

Esta versión genérica es equivalente a `Function<T, R>` de la API estándar. La diferencia es solo el nombre de la interfaz y del método (`transformar` vs `apply`).

---

## 13. Interfaces funcionales predefinidas en Java.

### Respuesta

Java proporciona en el paquete `java.util.function` un conjunto completo de interfaces funcionales predefinidas que cubren los casos más comunes:

| Interfaz | Método abstracto | Descripción |
|---|---|---|
| `Function<T, R>` | `R apply(T t)` | Transforma un valor de tipo `T` en tipo `R` |
| `BiFunction<T, U, R>` | `R apply(T t, U u)` | Transforma dos valores en uno |
| `Consumer<T>` | `void accept(T t)` | Consume un valor sin devolver nada |
| `BiConsumer<T, U>` | `void accept(T t, U u)` | Consume dos valores |
| `Supplier<T>` | `T get()` | Produce un valor sin recibir nada |
| `Predicate<T>` | `boolean test(T t)` | Evalúa una condición sobre un valor |
| `BiPredicate<T, U>` | `boolean test(T t, U u)` | Evalúa condición sobre dos valores |
| `UnaryOperator<T>` | `T apply(T t)` | Caso especial de `Function` donde entrada y salida son del mismo tipo |
| `BinaryOperator<T>` | `T apply(T t1, T t2)` | Caso especial de `BiFunction` donde todos los tipos son iguales |

Estas interfaces evitan tener que definir interfaces funcionales propias en la mayoría de los casos. El `Transformador<T, R>` genérico del ejercicio anterior es esencialmente idéntico a `Function<T, R>`.

---

## 14. `List.forEach` como versión funcional del bucle `for`.

### Respuesta

El método `forEach` recibe un `Consumer<T>` (una función que acepta un elemento y no devuelve nada) y lo ejecuta para cada elemento de la lista:

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(3, -1, 7, -4, 2, 0);

        numeros.forEach(n -> {
            if (n > 0) {
                System.out.println(n + " es positivo");
            }
        });
        // 3 es positivo
        // 7 es positivo
        // 2 es positivo
    }
}
```

El `forEach` es equivalente funcional de un bucle `for-each`, pero expresado como una operación sobre la colección que recibe el comportamiento como parámetro. Esto favorece un estilo más declarativo: se indica *qué hacer* con cada elemento, no *cómo recorrer* la estructura.

---

## 15. ¿Por qué `Consumer<? super T>` y no `Consumer<T>`? ¿Qué significa **PECS**?

### Respuesta

La firma de `forEach` usa `Consumer<? super T>` en lugar de `Consumer<T>` para mayor flexibilidad. Si la lista es `List<Integer>`, un `Consumer<Number>` o un `Consumer<Object>` también deberían poder consumir sus elementos (un `Integer` es un `Number`). Con `? super T`, se acepta cualquier consumidor cuyo tipo de entrada sea `T` o un supertipo de `T`.

**PECS** significa **Producer Extends, Consumer Super** y es la regla mnemotécnica para decidir qué wildcard usar:
- Si la estructura **produce** datos (se leen de ella) → `? extends T` (covarianza)
- Si la estructura **consume** datos (se escriben en ella) → `? super T` (contravarianza)

Aplicado al método `transformar`, se podría mejorar la firma así:

```java
public static String transformar(String texto, 
        Function<? super String, ? extends String> funcion) {
    return funcion.apply(texto);
}
```

El parámetro de entrada de la función es un **consumidor** de `String` (acepta `String` o supertipo), y la salida es un **productor** de `String` (devuelve `String` o subtipo). Esto hace el método más flexible sin perder seguridad de tipos.

---

## 16. Referencias a métodos.

### Respuesta

Una **referencia a método** permite obtener una referencia a un método existente (de un objeto o clase) y usarla como si fuera una función lambda.

En **JavaScript**:

```javascript
class Persona {
    constructor(nombre) { this.nombre = nombre; }
    saludar() { console.log(`Hola, soy ${this.nombre}`); }
}

const p = new Persona("Ana");
const saludar = p.saludar.bind(p); // Referencia al método, ligada al objeto
saludar(); // Hola, soy Ana
```

En **Java**:

```java
import java.util.function.Consumer;

class Persona {
    private String nombre;
    public Persona(String nombre) { this.nombre = nombre; }
    public void saludar() { System.out.println("Hola, soy " + nombre); }
}

public class Main {
    public static void main(String[] args) {
        Persona p = new Persona("Ana");
        Runnable saludar = p::saludar; // Referencia al método de instancia
        saludar.run(); // Hola, soy Ana
    }
}
```

En Java se usa el operador `::` para obtener la referencia. En JavaScript se usa `bind` para asegurar que el método queda ligado a la instancia correcta.

---

## 17. Tipos de referencias a método en Java.

### Respuesta

Existen cuatro tipos de referencias a método en Java:

**1. Referencia a método estático** (`Clase::metodoEstatico`):

```java
Function<String, Integer> parsear = Integer::parseInt;
System.out.println(parsear.apply("42")); // 42
```

**2. Referencia a constructor** (`Clase::new`):

```java
Function<String, Persona> crearPersona = Persona::new;
Persona p = crearPersona.apply("Luis"); // Equivale a new Persona("Luis")
```

**3. Referencia a método de instancia de un objeto concreto** (`objeto::metodo`):

```java
Persona ana = new Persona("Ana");
Runnable saludo = ana::saludar;
saludo.run(); // Hola, soy Ana
```

**4. Referencia a método de instancia sobre cualquier instancia** (`Clase::metodo`):

```java
Consumer<Persona> saludador = Persona::saludar;
saludador.accept(new Persona("María")); // Hola, soy María
saludador.accept(new Persona("Pedro")); // Hola, soy Pedro
```

En el caso 3, el método está ligado a un objeto específico (`ana`). En el caso 4, el primer argumento de la interfaz funcional se convierte en el receptor del método: `Persona::saludar` equivale a la lambda `p -> p.saludar()`.

---

## 18. Ordenar una lista de `Persona` con `Collections.sort`.

### Respuesta

Se define la clase `Persona` con nombre y edad, y se ordena usando dos enfoques:

```java
import java.util.*;

class Persona {
    private String nombre;
    private int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public String getNombre() { return nombre; }
    public int getEdad() { return edad; }

    @Override
    public String toString() { return nombre + " (" + edad + ")"; }
}
```

**Versión 1: comparación manual con lambda:**

```java
List<Persona> personas = new ArrayList<>(List.of(
    new Persona("Ana", 25),
    new Persona("Luis", 30),
    new Persona("María", 25),
    new Persona("Carlos", 30)
));

Collections.sort(personas, (p1, p2) -> {
    int cmpEdad = Integer.compare(p1.getEdad(), p2.getEdad());
    if (cmpEdad != 0) return cmpEdad;
    return p1.getNombre().compareTo(p2.getNombre());
});

System.out.println(personas);
// [Ana (25), María (25), Carlos (30), Luis (30)]
```

**Versión 2: usando `Comparator` con métodos encadenados:**

```java
Collections.sort(personas,
    Comparator.comparingInt(Persona::getEdad)
              .thenComparing(Persona::getNombre)
);

System.out.println(personas);
// [Ana (25), María (25), Carlos (30), Luis (30)]
```

La segunda versión es mucho más declarativa y legible. `Comparator.comparingInt` crea un comparador basado en la edad, y `thenComparing` añade un criterio secundario por nombre. Ambas usan referencias a método, lo que demuestra cómo la programación funcional en Java permite escribir código conciso y expresivo.
