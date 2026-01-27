<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Clases y Objetos". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: ninguno.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

### Respuesta

Las cuatro características fundamentales de la programación orientada a objetos son la abstracción, el encapsulamiento, la herencia y el polimorfismo. La abstracción permite simplificar la realidad seleccionando solo los atributos y comportamientos relevantes para el problema a resolver, ignorando los detalles innecesarios. El encapsulamiento consiste en ocultar el estado interno de un objeto y requerir que todas las interacciones se realicen a través de una interfaz definida, lo que protege la integridad de los datos, similar a cómo se usan funciones específicas para manipular un `FILE*` en C sin acceder directamente a sus campos.

La herencia favorece la reutilización de código permitiendo crear nuevas clases basadas en otras existentes, heredando sus atributos y comportamientos, de modo que se pueden establecer jerarquías "es-un". Por último, el polimorfismo es la capacidad que tienen objetos de diferentes clases de responder al mismo mensaje o llamada a función de maneras distintas, permitiendo escribir código genérico que puede trabajar con diferentes tipos de objetos sin conocer su clase exacta en tiempo de compilación.


## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

### Respuesta

Existen numerosos lenguajes que soportan el paradigma orientado a objetos. Entre los más populares en la industria actual se encuentran Java, que es puramente orientado a objetos en su diseño, y C++, que añade características de objetos al lenguaje C manteniendo compatibilidad.

Otros ejemplos destacados son Python, conocido por su sintaxis clara y tipado dinámico, y C#, desarrollado por Microsoft con una sintaxis similar a Java y C++. Todos estos lenguajes permiten definir clases y manipular objetos, aunque con diferentes reglas de gestión de memoria y sintaxis.


## 3. Los paradigmas anteriores a la POO, ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

### Respuesta

La programación estructurada es un paradigma que busca mejorar la claridad y calidad del código utilizando únicamente subrutinas y tres estructuras de control básicas: secuencia, selección (if/else) e iteración (bucles), eliminando el uso descontrolado de la instrucción `goto` que era común anteriormente. En C, esto se traduce en escribir funciones limpias y bucles bien definidos en lugar de saltos arbitrarios.

Por otro lado, la programación modular va un paso más allá y propone dividir el programa en módulos independientes e intercambiables, cada uno con una funcionalidad y responsabilidad específica. En C, esto se implementa típicamente separando el código en múltiples archivos `.c` (implementación) y `.h` (interfaz), donde cada módulo oculta sus detalles internos y expone solo lo necesario, sentando las bases conceptuales para lo que posteriormente sería el encapsulamiento en la orientación a objetos.

## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

### Respuesta

Un objeto en programación orientada a objetos se define principalmente por tres elementos: identidad, estado y comportamiento. La identidad es la propiedad que permite distinguir a un objeto de cualquier otro, independientemente de que sus contenidos sean idénticos, similar a cómo dos punteros en C pueden apuntar a direcciones de memoria distintas aunque la estructura almacenada contenga los mismos valores.

El estado se refiere al conjunto de valores de los atributos o variables internas del objeto en un momento dado, representando la "memoria" del objeto. Finalmente, el comportamiento está constituido por las operaciones o métodos que el objeto puede realizar y cómo reacciona a los mensajes que recibe, definiendo qué se puede hacer con él, análogo a las funciones que operan sobre una estructura específica en C.

## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

### Respuesta

Una clase es una plantilla o molde que define la estructura y el comportamiento de los objetos; se puede entender como una extensión del concepto de `struct` en C, pero que incluye no solo datos sino también las funciones (métodos) que operan sobre ellos. Un objeto, por contrapartida, es una instancia concreta de una clase, es decir, una variable específica creada siguiendo esa plantilla y que ocupa memoria. Instanciar es el proceso de crear ese objeto.

No es exactamente lo mismo clase que objeto: la clase es la definición abstracta (como el tipo `int` o una definición `struct Persona`), mientras que el objeto es la entidad concreta en ejecución (como una variable `int a = 5` o `struct Persona p`). Aunque la mayoría de lenguajes orientados a objetos usan clases, existen lenguajes basados en prototipos, como JavaScript, que no utilizan clases estrictas y crean objetos clonando otros objetos existentes.


## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

### Respuesta

En la mayoría de los lenguajes orientados a objetos modernos, como Java, los objetos se almacenan en el área de memoria conocida como *heap* (montículo), mientras que las referencias o punteros que apuntan a ellos se suelen guardar en el *stack* (pila) de ejecución. Esto difiere de lenguajes como C++, donde el programador puede elegir explícitamente crear objetos en el *stack* o en el *heap* utilizando `new`, dándole mayor control pero también mayor responsabilidad.

La recolección de basura (*Garbage Collection*) es un mecanismo automático de gestión de memoria presente en lenguajes como Java. Su función es detectar y liberar la memoria ocupada por objetos que ya no están siendo utilizados por el programa (no tienen referencias activas), evitando fugas de memoria. Esto contrasta con C, donde el programador debe liberar manualmente la memoria dinámica con `free()`; en Java, el recolector hace ese trabajo sucio por nosotros.


## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

### Respuesta

Un método es, en esencia, una función que pertenece a una clase y define el comportamiento de los objetos de esa clase. A diferencia de las funciones globales en C, los métodos tienen acceso directo a los atributos del objeto sobre el que se invocan (el contexto `this`).

La sobrecarga de métodos es una característica que permite definir múltiples métodos con el mismo nombre dentro de una misma clase, siempre y cuando su lista de parámetros sea diferente (en número o tipos de argumentos). Esto permite realizar operaciones similares con distintos tipos de datos (como un `print(int i)` y un `print(double d)`), algo que en C estándar no es posible directamente y requeriría nombres de función distintos.


## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

### Respuesta

```java
// Definición de la clase
class Punto {
    // Atributos con visibilidad por defecto
    double x;
    double y;

    // Método para calcular la distancia al origen
    double calculaDistanciaAOrigen() {
        // En Java se usa Math.sqrt y Math.pow
        return Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2));
    }
}

// Clase principal para probar el código
class TestPunto {
    public static void main(String[] args) {
        // Creación de una instancia (objeto) de Punto
        Punto miPunto = new Punto();
        
        // Asignación de valores a los atributos
        miPunto.x = 3.0;
        miPunto.y = 4.0;
        
        // Llamada al método y muestra del resultado
        double distancia = miPunto.calculaDistanciaAOrigen();
        System.out.println("La distancia es: " + distancia);
    }
}
```
Aquí se define la estructura de datos junto con su lógica asociada. Observará que no se necesitan punteros explícitos (`->`) para acceder a miembros; el operador `.` se encarga de dereferenciar automáticamente.


## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

### Respuesta

El punto de entrada de un programa Java es siempre un método llamado `main`, que debe ser público, estático y aceptar un array de Strings como argumento: `public static void main(String[] args)`. Esto es análogo a la función `main` en C, pero debe residir obligatoriamente dentro de una clase.

La palabra clave `static` indica que el miembro (atributo o método) pertenece a la clase en sí misma y no a una instancia específica; se puede invocar sin crear ningún objeto. No se usa solo para `main`; es útil para contadores globales, constantes o funciones de utilidad (como `Math.sqrt`). Cuando se combina con `final` (que equivale al `const` de C para variables), se crean constantes de clase inmutables, por ejemplo: `static final double PI = 3.14159;`.

## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

### Respuesta

Para ejecutar un programa Java desde la línea de comandos, primero se debe compilar el código fuente (archivo `.java`) utilizando el comando `javac NombreArchivo.java`. Esto no genera código máquina nativo directamente, sino un archivo intermedio `.class`. Posteriormente, se ejecuta el programa con el comando `java NombreClase` (sin extensión), que inicia la ejecución.

Java es un lenguaje compilado e interpretado (o compilado s JIT). El compilador `javac` traduce el código fuente a *byte-code* (los ficheros `.class`), que es un conjunto de instrucciones de bajo nivel independiente de la arquitectura. La Máquina Virtual de Java (JVM) es el software encargado de interpretar o compilar al vuelo (JIT) este *byte-code* a código máquina nativo de la CPU específica donde se ejecuta, permitiendo la portabilidad "Write Once, Run Anywhere".


## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

### Respuesta

El operador `new` se utiliza para asignar memoria dinámicamente para un nuevo objeto y devolver una referencia a él; es análogo a `malloc` en C, pero además invoca al constructor. El **constructor** es un bloque de código especial (similar a un método) que tiene el mismo nombre que la clase y se ejecuta automáticamente al usar `new`. Su propósito principal es inicializar los atributos del objeto, asegurando que este comience su vida en un estado válido.

Ejemplo de clase `Empleado` con constructor:

```java
class Empleado {
    String dni;
    String nombre;
    String apellidos;

    // Constructor: mismo nombre que la clase, sin tipo de retorno
    public Empleado(String d, String n, String a) {
        dni = d;
        nombre = n;
        apellidos = a;
    }
}
```


## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

### Respuesta

La palabra clave `this` representa una referencia al objeto actual que está ejecutando el método. Es fundamental para desambiguar entre atributos de la clase y parámetros de un método cuando tienen el mismo nombre. Conceptualmente, funciona de manera similar al puntero `this` en C++ (aunque en Java es una referencia, no un puntero manipulable). En Python, el equivalente explícito suele llamarse `self`.

Ejemplo en la clase `Punto` para un constructor:

```java
class Punto {
    double x;
    double y;

    // Constructor donde los parámetros ocultan a los atributos
    Punto(double x, double y) {
        this.x = x; // "this.x" se refiere al atributo, "x" al parámetro
        this.y = y;
    }
}
```


## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

### Respuesta

```java
class Punto {
    double x, y;

    // ... (constructor y otros métodos)

    // Método que calcula la distancia a otro punto
    double distanciaA(Punto otro) {
        // Usamos 'this' explícitamente para mayor claridad, aunque es opcional en x e y
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(Math.pow(dx, 2) + Math.pow(dy, 2));
    }
}
```

En este método, interactúan dos objetos: el objeto que "llama" o ejecuta el método (accesible vía `this`, o implícitamente) y el objeto pasado como argumento (`otro`).


## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función? 

### Respuesta

En Java, técnicamente todo paso de parámetros es **por valor**. Sin embargo, es crucial entender qué se pasa. Cuando se pasa un objeto (como `Punto`), se pasa por valor **la referencia** (la dirección de memoria) al objeto. Por tanto, si dentro del método modificamos los atributos del objeto referenciado (ej. `p.x = 10;`), **sí** se verán afectados fuera del método, ya que estamos operando sobre la misma instancia en memoria. Esto suele llamarse coloquialmente "paso por referencia", aunque no lo es estrictamente.

Por el contrario, si pasamos un tipo primitivo como `int`, se pasa una copia del valor. Si modificamos ese entero dentro de la función, el cambio **no** afecta a la variable original fuera, ya que son espacios de memoria independientes. Esto es idéntico a pasar un `int` por valor en C frente a pasar un puntero a una estructura.


## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

### Respuesta

El método `toString()` es un método especial definido en la clase base de todos los objetos en Java (`Object`), cuyo propósito es devolver una representación en cadena de texto del objeto. Es ampliamente utilizado para depuración y registros (logging). Cuando intentamos imprimir un objeto directamente (ej. `System.out.println(miPunto)`), Java invoca automáticamente este método. Otros lenguajes como Python tienen equivalentes (`__str__`).

Ejemplo en `Punto`:

```java
class Punto {
    double x, y;
    
    // Sobrescribimos el comportamiento por defecto
    public String toString() {
        return "Punto(" + x + ", " + y + ")";
    }
}
```


## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?

### Respuesta

Una clase y un `struct` de C comparten la característica básica de agrupar datos de tipos heterogéneos bajo un mismo nombre. De hecho, en C++, una `struct` es casi idéntica a una `class` salvo por la visibilidad por defecto.

Sin embargo, al `struct` clásico de C le falta un componente crucial para ser una clase: la asociación estricta con el comportamiento (métodos). En C, las funciones que operan sobre el struct están separadas de la definición de datos. Además, al `struct` le faltan mecanismos de control de acceso (public/private) y soporte para herencia y polimorfismo, que son pilares de la definición de clase en la orientación de objetos. Para convertir una variable de tipo `struct` en una verdadera "instancia", necesitaríamos vincularle inseparablemente sus operaciones y proteger su estado interno.


## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

### Respuesta

En C, podemos emular una clase definiendo un `struct Punto` y creando funciones que reciban explícitamente un puntero a esa estructura como primer argumento.

```c
#include <math.h>

typedef struct {
    double x;
    double y;
} Punto;

// Emulación del método: recibe el objeto explícitamente
double punto_calculaDistanciaAOrigen(Punto* p) {
    if (p == NULL) return 0.0;
    return sqrt(pow(p->x, 2) + pow(p->y, 2));
}
```

Lo que ocurre es que el puntero `this`, que es implícito en los métodos de Java o C++, aquí se hace totalmente explícito y visible (argumento `Punto* p`). En la llamada `miPunto.metodo()` de Java, el compilador está, bajo cuerda, realizando algo similar a `metodo(&miPunto)`, pasando la referencia del objeto automáticamente.
