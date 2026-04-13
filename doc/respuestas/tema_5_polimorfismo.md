<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El **polimorfismo** (del griego "muchas formas") es la capacidad de un objeto de comportarse de manera diferente según su tipo real en tiempo de ejecución, aunque se acceda a él a través de una referencia de un tipo más general (superclase). En la práctica, esto permite escribir código genérico que opera sobre objetos de distintas subclases sin necesidad de conocer de antemano cuál es el tipo concreto. Se trata de uno de los pilares fundamentales de la programación orientada a objetos, junto con la encapsulación y la herencia.

La **sobreescritura** (*overriding*) de métodos es el mecanismo que hace posible el polimorfismo: una subclase redefine un método heredado de su superclase con la misma firma (mismo nombre y mismos parámetros), sustituyendo o ampliando su comportamiento. De esta forma, cuando se invoca ese método sobre una referencia del tipo base, Java ejecuta la versión correspondiente al tipo real del objeto, no la de la referencia. Esto es lo que se denomina ligadura dinámica, concepto que se desarrollará en la siguiente pregunta.

---

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La **ligadura dinámica** (también llamada *enlace tardío* o *late binding*) consiste en que la decisión de qué implementación de un método se ejecuta se toma en **tiempo de ejecución**, en función del tipo real del objeto, y no en tiempo de compilación según el tipo de la referencia. Es exactamente lo que permite el polimorfismo: una referencia de tipo `Animal` puede apuntar a un `Perro` o a un `Gato`, y al llamar a `hacerSonido()` se ejecutará el método correcto para cada uno.

En **C++**, la ligadura dinámica no es el comportamiento por defecto. Para activarla es necesario declarar el método con la palabra clave `virtual` en la clase base. Sin `virtual`, C++ usa ligadura estática: la versión del método que se llama depende del tipo de la referencia/puntero en tiempo de compilación, no del tipo real del objeto. Esto obliga al programador a ser explícito sobre qué métodos deben ser polimórficos.

**Java**, por el contrario, aplica la ligadura dinámica de forma automática en todos los métodos de instancia que pueden sobreescribirse. No es necesario ninguna palabra clave especial: si una subclase sobreescribe un método, Java siempre invocará la versión más específica disponible en tiempo de ejecución. La única excepción son los métodos `static`, `private` y `final`, que usan ligadura estática porque no pueden sobreescribirse.

**Python** también usa ligadura dinámica de manera transparente y automática, sin necesidad de ningún modificador. Al ser un lenguaje dinámico y fuertemente basado en el *duck typing*, el polimorfismo es aún más flexible: dos clases sin ninguna relación de herencia pueden tener métodos con el mismo nombre y funcionarán de forma polimórfica simplemente porque comparten esa interfaz informal.

---

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

Se define la clase base `Soldado` con su método `saluda`, y las subclases `Zapador` y `Artillero`. Solo `Zapador` sobreescribe el método; `Artillero` hereda el comportamiento por defecto.

```java
class Soldado {
    public void saluda() {
        System.out.println("¡Soldado saludando!");
    }
}

class Zapador extends Soldado {
    @Override
    public void saluda() {
        System.out.println("¡Zapador saludando!");
    }
}

class Artillero extends Soldado {
    // No sobreescribe saluda(), hereda el de Soldado
}
```

Para ilustrar el polimorfismo, se crea un array de tipo `Soldado[]` que almacena objetos de distintos subtipos. Al iterar con una referencia de tipo `Soldado`, Java invoca automáticamente la versión correcta del método `saluda` según el tipo real de cada objeto en tiempo de ejecución:

```java
Soldado[] tropa = { new Zapador(), new Artillero(), new Zapador() };

for (Soldado s : tropa) {
    s.saluda(); // Ligadura dinámica: se ejecuta la versión del tipo real
}
// Salida:
// ¡Zapador saludando!
// ¡Soldado saludando!
// ¡Zapador saludando!
```

Es importante observar que la referencia del bucle es de tipo `Soldado`, pero Java no llama siempre a `Soldado.saluda()`. Consulta el tipo real del objeto en cada iteración y ejecuta la versión apropiada. Así es exactamente cómo funciona la ligadura dinámica combinada con el polimorfismo.

---

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Sí, es posible invocar el método de la clase base desde la sobreescritura de la subclase. Para ello se utiliza la palabra clave **`super`**, que hace referencia a la superclase directa. Así, en lugar de sustituir completamente el comportamiento, se puede reutilizar la lógica ya implementada en la clase padre y añadir el comportamiento adicional que se desee.

```java
class Zapador extends Soldado {
    @Override
    public void saluda() {
        super.saluda();  // Llama a Soldado.saluda()
        System.out.println("ZAPADOR A SUS ORDENES");
    }
}
```

Al ejecutar `saluda()` sobre un `Zapador`, primero se imprime el saludo genérico de `Soldado` (gracias a `super.saluda()`) y a continuación el mensaje específico del Zapador. El uso de `super` es muy habitual cuando se quiere extender un comportamiento sin duplicar código, respetando el principio DRY (*Don't Repeat Yourself*). Cabe destacar que `super` solo permite acceder al método del padre inmediato; no es posible "saltar" varios niveles de herencia con él.

---

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Para que Java reconozca una sobreescritura como tal, el método en la subclase debe tener exactamente la **misma firma** que en la superclase: mismo nombre y mismos tipos de parámetros (en el mismo orden). El tipo de retorno debe ser idéntico o un **subtipo** del tipo de retorno original (lo que se denomina *tipo de retorno covariante*, permitido desde Java 5). Además, el nivel de acceso del método sobreescrito no puede ser más restrictivo que el del método original: si el padre declara `public`, la subclase no puede declararlo `protected` o `private`.

La **sobrecarga** (*overloading*) es un concepto diferente: consiste en definir varios métodos con el **mismo nombre pero distinta lista de parámetros** (diferente número o tipos). La sobrecarga se resuelve en **tiempo de compilación** según los argumentos pasados en la llamada, y puede darse dentro de la misma clase o en subclases. La sobreescritura, en cambio, reemplaza la implementación de un método heredado y se resuelve en **tiempo de ejecución** mediante ligadura dinámica.

La anotación **`@Override`** le indica al compilador que el método tiene la intención de sobreescribir uno de la superclase. Si por error se escribe mal el nombre del método o se cambia la lista de parámetros, el compilador emitirá un error en lugar de crear silenciosamente una sobrecarga no intencionada. Por este motivo, su uso es siempre recomendable: actúa como una verificación automática que evita errores difíciles de detectar en tiempo de ejecución.

---

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Sí, efectivamente. En Java, **toda clase hereda implícitamente de `Object`**, que es la raíz de la jerarquía de clases. `Object` define métodos como `toString()`, `equals(Object o)` o `hashCode()`, que pueden sobreescribirse en cualquier clase propia. Cuando se hace esto, se está practicando polimorfismo desde los primeros programas, aunque no siempre se sea consciente de ello.

Por ejemplo, cuando se sobreescribe `toString()` en una clase `Persona`, y luego se llama a `System.out.println(persona)`, Java invoca internamente `persona.toString()`. Si la referencia fuera de tipo `Object`, seguiría ejecutándose la versión de `Persona`, no la de `Object`, gracias a la ligadura dinámica. Lo mismo ocurre con `equals`: librerías como las colecciones (`ArrayList`, `HashMap`, etc.) invocan `equals` sobre referencias de tipo `Object`, pero Java ejecutará la versión sobreescrita de la clase real.

Así pues, el polimorfismo no es algo que se "active" en una etapa avanzada del aprendizaje: está presente desde el momento en que se escribe la primera clase y se hereda de `Object`. Sobreescribir `toString` y `equals` es, de hecho, una de las primeras formas de polimorfismo que se aplican en la práctica.

---

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una **clase abstracta** es una clase que no puede instanciarse directamente. Su propósito es servir de base para otras clases, definiendo una estructura y comportamiento parcial común. Se declara con la palabra clave `abstract` antes de `class`. Un **método abstracto** es un método sin implementación (sin cuerpo), que obliga a las subclases concretas a proporcionar su propia implementación. También se declara con `abstract`. Una clase que tiene al menos un método abstracto debe ser ella misma abstracta.

No es posible crear instancias de una clase abstracta directamente (por ejemplo, `new Soldado()` daría un error de compilación si `Soldado` es abstracta). El propósito es justamente forzar a que sean las subclases concretas las que se instancien. Esto garantiza que ningún objeto pueda existir sin tener implementados todos los métodos abstractos.

La palabra clave `abstract` se coloca en la declaración de la clase y en la cabecera del método abstracto. El cuerpo del método abstracto se omite por completo (solo lleva `;`):

```java
abstract class Soldado {
    public void saluda() {
        System.out.println("¡Soldado saludando!");
    }

    // Método abstracto: cada subclase debe implementarlo
    public abstract void atacar();
}

class Zapador extends Soldado {
    @Override
    public void atacar() {
        System.out.println("El zapador coloca explosivos.");
    }
}

class Artillero extends Soldado {
    @Override
    public void atacar() {
        System.out.println("El artillero dispara el cañón.");
    }
}
```

Con este diseño, se puede escribir código que opera sobre `Soldado` de forma genérica (p.ej. recorrer un array de `Soldado[]` y llamar a `atacar()`), con la garantía de que todos los tipos concretos tendrán una implementación. Si se intentase crear una subclase de `Soldado` sin implementar `atacar()`, el compilador lo rechazaría, a menos que dicha subclase también se declare como abstracta.

---

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

En Java, `final` tiene dos usos relevantes para el polimorfismo. Aplicado a un **método**, impide que ninguna subclase pueda sobreescribirlo. La implementación queda bloqueada: cualquier intento de sobreescribir ese método en una subclase provocará un error de compilación. Esto implica que dicho método usa ligadura estática, ya que no puede haber ninguna versión alternativa en tiempo de ejecución.

Aplicado a una **clase**, `final` impide que se pueda heredar de ella en absoluto. Una clase `final` no puede tener subclases, y por tanto todos sus métodos son implícitamente no sobreescribibles. Esto supone una restricción total del polimorfismo para esa clase: nunca podrá haber subtipos de ella.

La relación con el polimorfismo es directamente inversa: `final` es una herramienta para **limitar o eliminar** el polimorfismo de forma deliberada. Se usa cuando el diseñador de la clase quiere garantizar que el comportamiento no sea alterado por herencia, ya sea por razones de seguridad, correctitud o rendimiento (la JVM puede optimizar mejor las llamadas a métodos `final` al saber en compilación qué versión se ejecutará).

Un ejemplo clásico en la API estándar de Java es la clase **`String`**, que está declarada como `final`. Esto impide que nadie pueda crear una subclase de `String` que altere su comportamiento (por ejemplo, que cambie cómo funciona `equals` o `hashCode`), lo cual es crucial para la seguridad y consistencia del lenguaje. Otro ejemplo es **`Integer`** y los demás *wrappers* de tipos primitivos (`Double`, `Boolean`, etc.).

---

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

Una **interfaz** en Java es un contrato que define un conjunto de métodos que una clase se compromete a implementar. Se declara con la palabra clave `interface` en lugar de `class`, y sus métodos son, por defecto, abstractos y públicos (sin necesidad de indicarlo explícitamente). Una clase que "firma" ese contrato lo hace con la palabra clave `implements`, y está obligada a proporcionar implementación para todos los métodos de la interfaz.

Aunque las interfaces y las clases abstractas comparten la idea de definir métodos sin implementación, existen diferencias importantes. Una clase abstracta puede tener atributos de instancia, constructores y métodos concretos (con cuerpo), además de los abstractos. Una interfaz tradicional no puede tener atributos de instancia (solo constantes `public static final`) ni constructores. Desde Java 8, las interfaces pueden tener métodos con implementación usando `default` y `static`, pero su naturaleza sigue siendo distinta a la de una clase abstracta.

La diferencia fundamental es que **Java solo permite herencia simple de clases** (una clase solo puede extender una superclase), pero **permite implementar múltiples interfaces**. Esto es clave para superar la limitación de la herencia simple y añadir distintas capacidades a una clase:

```java
interface Nadador {
    void nadar();
}

interface Volador {
    void volar();
}

class Pato implements Nadador, Volador {
    @Override
    public void nadar() { System.out.println("El pato nada."); }

    @Override
    public void volar() { System.out.println("El pato vuela."); }
}
```

Esto permite usar referencias de tipo `Nadador` o `Volador` de forma polimórfica, según el contexto que interese, aunque el objeto real sea siempre un `Pato`.

---

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

Se define `Punto` como clase abstracta con el método `calcularDistanciaA` abstracto. Las subclases `Punto2D` y `Punto3D` lo implementan usando la fórmula euclídea correspondiente. El operador `instanceof` comprueba el tipo real del argumento antes del *downcasting*, evitando `ClassCastException`:

```java
abstract class Punto {
    public abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
    private double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("Solo se puede calcular distancia entre Punto2D.");
        }
        Punto2D p = (Punto2D) otro; // downcasting
        return Math.sqrt(Math.pow(p.x - this.x, 2) + Math.pow(p.y - this.y, 2));
    }
}

class Punto3D extends Punto {
    private double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("Solo se puede calcular distancia entre Punto3D.");
        }
        Punto3D p = (Punto3D) otro; // downcasting
        return Math.sqrt(Math.pow(p.x - this.x, 2) + Math.pow(p.y - this.y, 2) + Math.pow(p.z - this.z, 2));
    }
}
```

La clase `Linea` trabaja exclusivamente con referencias de tipo `Punto`, sin conocer si son 2D o 3D. La lógica de cálculo de distancia queda totalmente delegada en los objetos concretos gracias al polimorfismo:

```java
class Linea {
    private Punto inicio;
    private Punto fin;

    public Linea(Punto inicio, Punto fin) {
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.calcularDistanciaA(fin);
    }
}
```

Y un ejemplo de uso:

```java
Linea linea2D = new Linea(new Punto2D(0, 0), new Punto2D(3, 4));
System.out.println(linea2D.longitud()); // 5.0

Linea linea3D = new Linea(new Punto3D(0, 0, 0), new Punto3D(1, 1, 1));
System.out.println(linea3D.longitud()); // ~1.732
```

Este diseño ilustra una de las grandes ventajas del polimorfismo en OOP: `Linea` es completamente independiente de la dimensionalidad de sus puntos. Si en el futuro se añadiera `Punto4D`, `Linea` no necesitaría ningún cambio, siempre que `Punto4D` implemente correctamente `calcularDistanciaA`. Esto es el principio abierto/cerrado (*Open/Closed Principle*): el código está abierto para extensión pero cerrado para modificación.

---

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

En Java, las interfaces también pueden heredar de otras interfaces, usando de nuevo la palabra clave `extends`. Una interfaz hija hereda todos los métodos abstractos de su interfaz padre, y puede añadir nuevos métodos propios. Cualquier clase que implemente la interfaz hija estará obligada a implementar tanto los métodos heredados como los nuevos.

A diferencia de las clases (que solo pueden heredar de una única superclase), **las interfaces sí permiten herencia múltiple**: una interfaz puede extender varias interfaces a la vez, separándolas con comas. Esto no genera el clásico "problema del diamante" de C++ porque las interfaces (en su forma abstracta) no tienen estado ni implementación que entre en conflicto.

El ejemplo pedido ilustra cómo `FicheroEscribible` extiende `Fichero` añadiendo capacidades de escritura y eliminación:

```java
interface Fichero {
    /**
     * Lee y devuelve el contenido completo del fichero como String.
     */
    String leerContenido();
}

interface FicheroEscribible extends Fichero {
    /**
     * Escribe el contenido dado en el fichero.
     */
    void escribirContenido(String contenido);

    /**
     * Elimina el fichero del sistema.
     */
    void eliminar();
}

// Una implementación concreta debe cumplir con ambas interfaces
class FicheroTexto implements FicheroEscribible {
    private String nombre;
    private String contenido;

    public FicheroTexto(String nombre) {
        this.nombre = nombre;
        this.contenido = "";
    }

    @Override
    public String leerContenido() {
        return contenido;
    }

    @Override
    public void escribirContenido(String contenido) {
        this.contenido = contenido;
        System.out.println("Contenido escrito en " + nombre);
    }

    @Override
    public void eliminar() {
        System.out.println("Fichero " + nombre + " eliminado.");
    }
}
```

Gracias a la herencia de interfaces, es posible manejar un `FicheroTexto` como si fuera un simple `Fichero` (accediendo solo a la lectura) o como un `FicheroEscribible` (accediendo también a escritura y eliminación), de forma polimórfica. Esto permite diseñar APIs donde distintos componentes del sistema reciben solo el nivel de acceso que necesitan, respetando el principio de mínimo privilegio.
