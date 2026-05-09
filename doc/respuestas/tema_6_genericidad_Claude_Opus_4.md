<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

En C, se puede utilizar `void*` para crear un array que almacene punteros a cualquier tipo de dato, ya que `void*` es un puntero genérico que puede apuntar a cualquier dirección de memoria. En Java, dado que toda clase hereda de `Object`, se puede crear un array de `Object` que almacene cualquier objeto.

Ejemplo en C con `void*`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    void** elementos;
    int tamano;
    int capacidad;
} Contenedor;

Contenedor* crear(int capacidad) {
    Contenedor* c = malloc(sizeof(Contenedor));
    c->elementos = malloc(sizeof(void*) * capacidad);
    c->tamano = 0;
    c->capacidad = capacidad;
    return c;
}

void agregar(Contenedor* c, void* elemento) {
    if (c->tamano < c->capacidad) {
        c->elementos[c->tamano++] = elemento;
    }
}

void* obtener(Contenedor* c, int indice) {
    return c->elementos[indice];
}
```

Ejemplo equivalente en Java con `Object`:

```java
class Contenedor {
    private Object[] elementos;
    private int tamano;

    public Contenedor(int capacidad) {
        this.elementos = new Object[capacidad];
        this.tamano = 0;
    }

    public void agregar(Object elemento) {
        elementos[tamano++] = elemento;
    }

    public Object obtener(int indice) {
        return elementos[indice];
    }
}
```

Ambas versiones permiten almacenar cualquier tipo de dato, pero a costa de perder la información de tipo, lo que obliga a realizar conversiones manuales (*casts*) al recuperar los datos.

---

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La **programación genérica** es un paradigma de programación en el que se escriben algoritmos y estructuras de datos de forma independiente al tipo de dato concreto con el que operan. El objetivo es crear código reutilizable que funcione con cualquier tipo, evitando duplicar la lógica para cada tipo específico. En lugar de escribir una lista para `int`, otra para `String` y otra para `Persona`, se escribe una única implementación genérica.

El ejemplo anterior (con `void*` en C y `Object` en Java) es, efectivamente, una forma **básica y primitiva** de programación genérica. Permite almacenar cualquier tipo de dato en la misma estructura. Sin embargo, es una forma rudimentaria porque carece de seguridad de tipos: el compilador no puede verificar que se almacenan y recuperan datos del tipo correcto, lo que traslada los errores al tiempo de ejecución. Los mecanismos modernos de genericidad (como los *generics* de Java o los *templates* de C++) resuelven estas deficiencias.

---

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El problema fundamental es la **pérdida de información de tipo**. Cuando se almacena un dato en un array de `void*` o de `Object`, el compilador pierde toda noción de cuál era el tipo original. Al recuperar el dato, es responsabilidad del programador hacer el *cast* correcto. Si se comete un error (por ejemplo, almacenar un `String` y luego hacer un *cast* a `Integer`), en C se obtiene comportamiento indefinido, y en Java se lanza una `ClassCastException` en tiempo de ejecución.

Otro problema grave es que **no hay forma de restringir los tipos que se almacenan**. Un contenedor basado en `Object` acepta cualquier cosa, por lo que nada impide mezclar tipos incompatibles en la misma colección. Esto elimina la ventaja principal de un lenguaje con tipado estático: detectar errores en tiempo de compilación. En esencia, se ha convertido un error detectable por el compilador en un bug potencial en producción.

```java
Contenedor c = new Contenedor(10);
c.agregar("Hola");
c.agregar(42);

// El compilador NO detecta este error; falla en ejecución
String s = (String) c.obtener(1); // ClassCastException: Integer → String
```

---

## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los **parámetros de tipo** son variables que representan tipos (en lugar de valores) y que se especifican al definir una clase, interfaz o método. Se declaran entre ángulos (`< >`) y actúan como *placeholders* que serán sustituidos por tipos concretos cuando se use la clase o método. Por convención, se utilizan letras mayúsculas simples: `T` (tipo), `E` (elemento), `K` (clave), `V` (valor), etc.

Gracias a los parámetros de tipo, el compilador puede realizar el **chequeo de tipos en tiempo de compilación**. Cuando se instancia una clase genérica con un tipo concreto, por ejemplo `Contenedor<String>`, el compilador sabe que solo se pueden insertar y extraer objetos de tipo `String`, eliminando la necesidad de *casts* manuales y evitando los errores en tiempo de ejecución que se producían con `Object` o `void*`.

```java
// T es un parámetro de tipo
class Contenedor<T> {
    private Object[] elementos; // internamente sigue siendo Object
    private int tamano;

    public void agregar(T elemento) { elementos[tamano++] = elemento; }

    @SuppressWarnings("unchecked")
    public T obtener(int indice) { return (T) elementos[indice]; }
}

// Al usar Contenedor<String>, T se sustituye por String
Contenedor<String> c = new Contenedor<>();
c.agregar("Hola");     // OK
// c.agregar(42);       // ERROR de compilación: int no es String
String s = c.obtener(0); // No necesita cast
```

---

## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

En Java, se utiliza la interfaz `List` con generics. Al declarar `List<String>`, el compilador garantiza que solo se pueden añadir y extraer objetos de tipo `String`:

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> nombres = new ArrayList<>();
        nombres.add("Ana");
        nombres.add("Luis");
        nombres.add("María");

        for (String nombre : nombres) {
            // 'nombre' ya es String, sin necesidad de cast
            System.out.println(nombre.toUpperCase());
        }
    }
}
```

En C++, se usa `std::vector` con templates. El mecanismo es análogo: al declarar `std::vector<std::string>`, el compilador genera código específico para `std::string`:

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main() {
    std::vector<std::string> nombres;
    nombres.push_back("Ana");
    nombres.push_back("Luis");
    nombres.push_back("María");

    for (const std::string& nombre : nombres) {
        // 'nombre' es std::string con seguridad
        std::cout << nombre << std::endl;
    }
    return 0;
}
```

En ambos casos, si se intentase insertar un valor de tipo incorrecto (por ejemplo un entero), el compilador lo detectaría inmediatamente como un error. Esta es la gran mejora respecto a usar `void*` o `Object`: la seguridad de tipos se comprueba en compilación, no en ejecución.

---

## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

No, C++ y Java implementan la programación genérica de forma fundamentalmente distinta. En **C++**, cuando se usa un template con un tipo concreto (por ejemplo `vector<int>` y `vector<string>`), el compilador genera una **copia completa del código** para cada tipo utilizado. Esto se conoce como **instanciación de plantillas** (*template instantiation*): si se usan 5 tipos distintos, se generan 5 versiones distintas del código máquina. La ventaja es que el código generado es especializado y potencialmente más eficiente; la desventaja es que puede aumentar el tamaño del binario.

En **Java**, el mecanismo es completamente diferente y se conoce como **type erasure** (borrado de tipos). El compilador verifica la corrección de tipos en compilación, pero luego **elimina toda la información de los parámetros de tipo** del bytecode generado. Internamente, todos los parámetros de tipo se reemplazan por `Object` (o por su cota superior si se ha definido con `extends`). Solo existe **una única versión** de la clase compilada, independientemente de cuántos tipos distintos se usen. El compilador inserta automáticamente los *casts* necesarios en el código que usa la clase genérica.

Esto tiene consecuencias prácticas importantes en Java: no se puede hacer `new T()`, ni `new T[]`, ni `instanceof T`, porque `T` no existe en tiempo de ejecución. Toda la información genérica es puramente una ayuda al compilador que desaparece una vez generado el bytecode.

---

## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

La clase `Par` utiliza dos parámetros de tipo `A` y `B` para representar el tipo del primer y segundo elemento respectivamente:

```java
public class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() { return primero; }
    public B getSegundo() { return segundo; }

    @Override
    public String toString() {
        return "(" + primero + ", " + segundo + ")";
    }
}
```

Se puede usar esta clase para devolver múltiples valores con tipos distintos desde un único método. En el siguiente ejemplo, se calcula la media y la desviación típica de un array de `double`, devolviendo ambos valores en un `Par<Double, Double>`:

```java
public class Estadistica {
    public static Par<Double, Double> mediaYDesviacion(double[] datos) {
        double suma = 0;
        for (double d : datos) suma += d;
        double media = suma / datos.length;

        double sumaDesv = 0;
        for (double d : datos) sumaDesv += Math.pow(d - media, 2);
        double desviacion = Math.sqrt(sumaDesv / datos.length);

        return new Par<>(media, desviacion);
    }

    public static void main(String[] args) {
        double[] notas = {7.5, 8.0, 6.5, 9.0, 5.5};
        Par<Double, Double> resultado = mediaYDesviacion(notas);

        System.out.println("Media: " + resultado.getPrimero());       // 7.3
        System.out.println("Desviación: " + resultado.getSegundo());  // ~1.2
    }
}
```

Nótese que `Par` es completamente reutilizable: `Par<String, Integer>`, `Par<Persona, List<Nota>>`, etc. Es un patrón tan útil que en la propia API de JavaFX existe `javafx.util.Pair`, y en muchas librerías de terceros como Apache Commons hay implementaciones equivalentes.

---

## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

Primero, la versión con `Object`. Funciona pero requiere *downcasting* al usar el resultado y no garantiza que ambos objetos sean del mismo tipo:

```java
public static Object seleccionaUno(Object a, Object b) {
    return Math.random() < 0.5 ? a : b;
}

// Uso:
String elegido = (String) seleccionaUno("Hola", "Mundo");   // Cast manual obligatorio
Object mixto = seleccionaUno("Hola", 42);                    // Compila sin error: tipos distintos
```

Ahora la versión con un **método genérico**. El parámetro de tipo `<T>` se declara antes del tipo de retorno:

```java
public static <T> T seleccionaUno(T a, T b) {
    return Math.random() < 0.5 ? a : b;
}

// Uso:
String elegido = seleccionaUno("Hola", "Mundo"); // Sin cast: devuelve String directamente
// seleccionaUno("Hola", 42);                     // El compilador infiere T como Object (no fuerza error)
```

Las ventajas del método genérico son claras: **(i)** el tipo de retorno coincide con el tipo de los argumentos, por lo que no se necesita *downcasting*; **(ii)** el compilador infiere `T` a partir de los argumentos y, si los tipos no coinciden, `T` se infiere como su ancestro común más específico. Si se quiere forzar estrictamente que ambos sean del mismo tipo, se puede ser explícito en la invocación: `Utilidades.<String>seleccionaUno("Hola", 42)` produciría un error de compilación.

---

## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí, se pueden establecer restricciones sobre los parámetros de tipo usando la palabra clave `extends`. La sintaxis `<T extends Number>` indica que `T` debe ser `Number` o cualquier subclase suya (`Integer`, `Double`, `Float`, etc.). Esto se denomina **cota superior** (*upper bound*).

**Solución 1: usando `Number` directamente** (sin generics):

```java
class Punto {
    private Number x, y;

    public Punto(Number x, Number y) { this.x = x; this.y = y; }
    public Number getX() { return x; }
    public Number getY() { return y; }

    public double calcularDistanciaA(Punto otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

**Solución 2: usando generics con cota superior**:

```java
class Punto<T extends Number> {
    private T x, y;

    public Punto(T x, T y) { this.x = x; this.y = y; }
    public T getX() { return x; }
    public T getY() { return y; }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Respecto al *type erasure*, cuando se define `<T extends Number>`, tras la compilación `T` se reemplaza por `Number` (la cota superior), no por `Object`. Es decir, el bytecode generado es prácticamente idéntico al de la solución 1. La diferencia reside exclusivamente en la **comprobación en tiempo de compilación**: la versión con generics permite saber exactamente con qué tipo de número se trabaja (`Punto<Integer>`, `Punto<Double>`, etc.).

---

## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

La **solución sin generics** (con `Number`) permite crear un punto con coordenadas de tipos distintos sin ningún problema, ya que ambas son simplemente `Number`:

```java
Punto p = new Punto(3, 2.5); // Integer y Double mezclados: compila sin problema
```

La **solución con generics** (`Punto<T extends Number>`) **no permite mezclar tipos**, porque `T` debe ser un único tipo concreto. Si se declara `Punto<Integer>`, ambas coordenadas deben ser `Integer`; si se declara `Punto<Double>`, ambas deben ser `Double`. Intentar mezclar produce un error de compilación:

```java
Punto<Integer> p1 = new Punto<>(3, 5);      // OK
Punto<Double> p2 = new Punto<>(1.0, 2.5);   // OK
// Punto<Integer> p3 = new Punto<>(3, 2.5);  // ERROR: 2.5 no es Integer
```

En cuanto al tipo de retorno de `getX()`: en la solución sin generics, devuelve `Number`, por lo que para obtener el tipo concreto se necesita un *downcast* (`Integer x = (Integer) p.getX()`). En la solución con generics, devuelve directamente `T`: si se tiene un `Punto<Integer>`, `getX()` devuelve `Integer` sin necesidad de *cast*. Esto demuestra que los generics no solo refuerzan lo que se introduce, sino también lo que se extrae, proporcionando un chequeo de tipos completo y bidireccional.

---

## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

La clave es añadir un parámetro de tipo a la interfaz `Punto` que represente "el propio tipo" (*self-type*). Se usa el patrón conocido como **CRTP** (*Curiously Recurring Template Pattern*), donde cada implementación se pasa a sí misma como parámetro de tipo:

```java
public interface Punto<T extends Punto<T>> {
    public double distanciaA(T p);
}
```

Ahora `Punto2D` implementa `Punto<Punto2D>`, lo que hace que el método `distanciaA` reciba directamente un `Punto2D`:

```java
public class Punto2D implements Punto<Punto2D> {
    private final double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2));
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x; this.y = y; this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p) {
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2)
                + Math.pow(z - p.z, 2));
    }
}
```

Con este diseño, se han eliminado tanto el `instanceof` como el *downcasting*. El compilador garantiza en tiempo de compilación que `Punto2D.distanciaA` solo acepta otro `Punto2D`, y `Punto3D.distanciaA` solo acepta otro `Punto3D`. Si se intenta calcular la distancia entre un `Punto2D` y un `Punto3D`, el error se detecta directamente durante la compilación, no en ejecución con una excepción.

---

## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

**`List<String>` NO es subtipo de `List<Object>`**, a pesar de que `String` sí es subtipo de `Object`. En cambio, **`String[]` SÍ es subtipo de `Object[]`**. Esta diferencia es una decisión de diseño deliberada de Java.

Los **arrays en Java son covariantes**: si `A` es subtipo de `B`, entonces `A[]` es subtipo de `B[]`. Esto permite pasar un `String[]` donde se espera un `Object[]`, pero introduce un peligro: si se intenta insertar un tipo incompatible a través de la referencia general, Java lanza un `ArrayStoreException` en tiempo de ejecución:

```java
Object[] array = new String[3]; // Compila: String[] es subtipo de Object[]
array[0] = "Hola";              // OK
array[1] = 42;                  // Compila, pero lanza ArrayStoreException en ejecución
```

Los **generics en Java son invariantes**: `List<String>` y `List<Object>` son tipos completamente independientes, sin relación de herencia. Esto se hizo precisamente para evitar el problema de los arrays: al no permitir la asignación, el compilador garantiza que no se puedan insertar tipos incompatibles.

En terminología formal: un tipo genérico es **covariante** si preserva la relación de subtipado (`A <: B` implica `G<A> <: G<B>`), **contravariante** si la invierte (`A <: B` implica `G<B> <: G<A>`), e **invariante** si no hay relación de subtipado entre `G<A>` y `G<B>` independientemente de la relación entre `A` y `B`. Los generics de Java son invariantes por defecto, pero permiten recuperar covarianza y contravarianza de forma controlada mediante *wildcards*.

---

## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un **wildcard** (`?`) representa un tipo desconocido. Se utiliza cuando se quiere trabajar con un tipo genérico sin especificar exactamente el parámetro de tipo. Java ofrece tres variantes: `?` (sin restricción), `? extends T` (cota superior, covarianza) y `? super T` (cota inferior, contravarianza).

**`List<? extends T>`** significa "una lista de algún tipo desconocido que es `T` o un subtipo de `T`". Se usa cuando se quiere **leer** de la lista: se pueden extraer elementos como tipo `T`, pero no se pueden insertar (porque no se sabe el tipo exacto). Esto proporciona **covarianza**.

**`List<? super T>`** significa "una lista de algún tipo desconocido que es `T` o un supertipo de `T`". Se usa cuando se quiere **escribir** en la lista: se pueden insertar elementos de tipo `T`, pero al leer solo se obtiene `Object`. Esto proporciona **contravarianza**.

**(i) Ejemplo con `? extends` — lectura (suma de números):**

```java
public static double sumar(List<? extends Number> numeros) {
    double total = 0;
    for (Number n : numeros) {
        total += n.doubleValue();
    }
    return total;
}

// Funciona con List<Integer>, List<Double>, etc.
List<Integer> enteros = List.of(1, 2, 3);
List<Double> reales = List.of(1.5, 2.5);
System.out.println(sumar(enteros)); // 6.0
System.out.println(sumar(reales));  // 4.0
```

**(ii) Ejemplo con `? super` — escritura (añadir enteros):**

```java
public static void agregarEnteros(List<? super Integer> lista) {
    lista.add(10);
    lista.add(20);
    lista.add(30);
}

// Funciona con List<Integer>, List<Number>, List<Object>
List<Number> numeros = new ArrayList<>();
agregarEnteros(numeros); // OK: Number es supertipo de Integer
```

Esta dualidad se resume con la regla mnemotécnica **PECS** (*Producer Extends, Consumer Super*): si la lista produce datos (se lee de ella), se usa `extends`; si consume datos (se escribe en ella), se usa `super`.
