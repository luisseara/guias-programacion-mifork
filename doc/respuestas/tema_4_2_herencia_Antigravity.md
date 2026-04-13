<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

La **herencia** es un mecanismo fundamental de la orientación a objetos que permite definir una clase (llamada subclase o clase hija) a partir de otra ya existente (llamada superclase o clase base). La relación se expresa como **"A es-un B"**: si `Artillero` hereda de `Soldado`, entonces todo `Artillero` **es un** `Soldado`. Esto tiene dos implicaciones principales que se explican a continuación.

La primera implicación es la **compatibilidad de tipos**: si `Artillero` es-un `Soldado`, entonces en cualquier lugar donde se espere un `Soldado` se puede usar un `Artillero`. Esto significa que se puede asignar un objeto de tipo `Artillero` a una referencia de tipo `Soldado`, o pasar un `Artillero` como argumento a un método que reciba `Soldado`. La segunda implicación es la **herencia de estado y comportamiento**: la subclase hereda automáticamente todos los atributos (estado) y métodos (comportamiento) de la superclase. Es decir, `Artillero` y `Zapador` poseen el atributo `nombre` y el método `saludar()` sin necesidad de volver a escribirlos.

A continuación se muestra un ejemplo completo en Java:

```java
// Superclase
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("¡Soy el soldado " + nombre + ", a sus órdenes!");
    }
}

// Subclase Artillero
public class Artillero extends Soldado {
    private int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }
}

// Subclase Zapador
public class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }
}
```

Para ilustrar la **compatibilidad de tipos**, se puede crear un array de `Soldado` y almacenar en él objetos de distintos subtipos. Al recorrer el array y llamar a `saludar()`, todos responden correctamente porque todos "son un" `Soldado`:

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] pelotón = new Soldado[3];
        pelotón[0] = new Artillero("García", 5);
        pelotón[1] = new Zapador("López", 3);
        pelotón[2] = new Artillero("Martín", 8);

        for (Soldado s : pelotón) {
            s.saludar();
        }
    }
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### Respuesta

Al crear un objeto de una subclase como `Artillero`, se ejecutan **dos constructores** (o más, si la cadena de herencia es más larga): primero el de la superclase (`Soldado`) y después el de la subclase (`Artillero`). Es decir, la construcción se realiza de **arriba hacia abajo** en la jerarquía: se construye primero la parte del `Soldado` y luego la parte específica del `Artillero`. Esto garantiza que la parte heredada esté completamente inicializada antes de que la subclase añada su propia inicialización.

La palabra clave **`super`** dentro de un constructor se utiliza para invocar explícitamente al constructor de la superclase. Se escribe como `super(argumentos)` y **debe ser la primera instrucción** del constructor de la subclase. En el ejemplo, `Artillero` llama a `super(nombre)` para que el constructor de `Soldado` inicialice el atributo `nombre`.

Si la clase base **no tiene visible un constructor sin parámetros** (bien porque se definió uno con parámetros y no se escribió uno vacío, o bien porque el constructor vacío es `private`), entonces es **obligatorio** llamar a `super(...)` con los argumentos adecuados en cada constructor de la subclase. Si no se hace, el compilador dará un error, ya que intentará llamar al constructor sin parámetros de la superclase automáticamente y este no existirá o no será accesible.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

Sí, los atributos privados de la superclase **sí forman parte** de la instancia de la subclase en memoria. Cuando se crea un objeto `Artillero`, ese objeto contiene en memoria tanto el atributo `nombre` (definido en `Soldado`) como el atributo `cohetes` (definido en `Artillero`). El objeto es un bloque de memoria que incluye toda la información de toda la cadena de herencia.

Sin embargo, que estén **en memoria** no significa que sean **accesibles desde el código de la subclase**. El modificador `private` impide que la subclase acceda directamente al atributo. Es decir, dentro del código de `Artillero` no se puede escribir `this.nombre` directamente porque `nombre` es `private` en `Soldado`. Para acceder a él se necesitaría un método público o protegido en `Soldado` (como un getter `getNombre()`), o bien declarar el atributo como `protected` en lugar de `private`.

En resumen, hay que distinguir entre la **existencia física** del atributo en el objeto (que siempre ocurre) y la **visibilidad** desde el código de la subclase (que depende del modificador de acceso). La encapsulación se mantiene: el atributo está ahí, pero la subclase solo puede interactuar con él a través de la interfaz que la superclase exponga.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

La compatibilidad de tipos proporcionada por la herencia tiene una implicación directa en la **extensibilidad** del código: se pueden añadir nuevos subtipos sin modificar el código ya escrito que trabaja con el supertipo. Esto es una consecuencia directa del principio **Abierto/Cerrado** (Open/Closed Principle): el código está abierto a extensión (se pueden añadir nuevas subclases) pero cerrado a modificación (no se cambia el código existente).

Por ejemplo, supongamos que se añade un nuevo tipo de soldado, un `Francotirador`:

```java
public class Francotirador extends Soldado {
    private int balas;

    public Francotirador(String nombre, int balas) {
        super(nombre);
        this.balas = balas;
    }

    public int getBalas() {
        return balas;
    }
}
```

El código del `main` que recorre el array de soldados y pide que saluden **no necesita ninguna modificación**:

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] pelotón = new Soldado[4];
        pelotón[0] = new Artillero("García", 5);
        pelotón[1] = new Zapador("López", 3);
        pelotón[2] = new Artillero("Martín", 8);
        pelotón[3] = new Francotirador("Pérez", 20); // nuevo tipo

        for (Soldado s : pelotón) {
            s.saludar(); // funciona para todos, incluido el nuevo tipo
        }
    }
}
```

Como `Francotirador` es-un `Soldado`, encaja perfectamente en el array y responde a `saludar()`. El bucle existente no se ha tocado. Esto demuestra cómo la herencia permite que el sistema crezca de forma modular y mantenible.


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

Sí, en Java se puede tener una **referencia del supertipo** que apunte a un objeto real de un subtipo. Por ejemplo, `Soldado s = new Artillero("García", 5);` es perfectamente válido. Sin embargo, a través de esa referencia de tipo `Soldado`, **solo se pueden invocar los métodos declarados en `Soldado`**. No se puede llamar a `s.getCohetes()` directamente porque el compilador solo conoce el tipo de la referencia (`Soldado`), no el tipo real del objeto en tiempo de ejecución.

El **upcasting** es la conversión de una referencia de un subtipo a una referencia del supertipo. Es implícita y segura, ya que todo `Artillero` es-un `Soldado`. El **downcasting** es la operación inversa: convertir una referencia del supertipo al subtipo, por ejemplo `Artillero a = (Artillero) s;`. Esta operación es **explícita** (requiere un cast) y **potencialmente peligrosa**: si el objeto real no es del tipo al que se hace el cast, se lanza una `ClassCastException` en tiempo de ejecución.

Para evitar errores al hacer downcasting, se utiliza el operador **`instanceof`**, que permite comprobar en tiempo de ejecución si un objeto es realmente una instancia de una clase determinada. Devuelve `true` o `false`. A continuación se muestra un ejemplo:

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] pelotón = new Soldado[3];
        pelotón[0] = new Artillero("García", 5);
        pelotón[1] = new Zapador("López", 3);
        pelotón[2] = new Artillero("Martín", 8);

        for (Soldado s : pelotón) {
            s.saludar();
            if (s instanceof Artillero) {
                Artillero a = (Artillero) s; // downcasting seguro
                System.out.println("  -> Tiene " + a.getCohetes() + " cohetes.");
            }
        }
    }
}
```

En este ejemplo, solo se hace el downcasting a `Artillero` cuando `instanceof` confirma que el objeto real es efectivamente un `Artillero`, evitando así posibles excepciones.


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

El acceso **protegido** (`protected`) es un nivel de visibilidad intermedio entre `private` y `public`. Un miembro declarado como `protected` es accesible desde la propia clase, desde las subclases (independientemente del paquete en el que estén) y desde otras clases del mismo paquete. En Java se implementa anteponiendo la palabra clave `protected` a la declaración del atributo o método.

Este modificador es útil cuando se desea que las subclases puedan acceder directamente a ciertos atributos o métodos de la superclase, sin exponerlos al público general. Sin embargo, debe usarse con moderación, ya que rompe parcialmente la encapsulación: cualquier clase del mismo paquete también tendrá acceso, y las subclases quedan acopladas a la representación interna de la superclase.

A continuación se muestra el ejemplo modificado donde `nombre` se declara como `protected` en `Soldado`, permitiendo que `Zapador` lo use directamente en su método `ponerMina()`:

```java
public class Soldado {
    protected String nombre; // ahora es protegido

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("¡Soy el soldado " + nombre + ", a sus órdenes!");
    }
}

public class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }

    public void ponerMina() {
        if (minas > 0) {
            minas--;
            // Se accede directamente a 'nombre' porque es protected
            System.out.println(nombre + " ha colocado una mina. Quedan " + minas);
        } else {
            System.out.println(nombre + " no tiene minas disponibles.");
        }
    }
}
```


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

En muchos lenguajes orientados a objetos existe una **clase base universal** de la que heredan, directa o indirectamente, todas las demás clases. No ocurre en todos los lenguajes: por ejemplo, en C++ no existe una clase base universal obligatoria (aunque se puede diseñar una manualmente); cada jerarquía de clases es independiente.

En **Java**, sí existe esa clase base universal: se llama **`Object`** (paquete `java.lang`). Toda clase en Java hereda implícitamente de `Object` si no se indica explícitamente otra superclase. Es decir, cuando se escribe `public class Soldado { ... }`, es equivalente a escribir `public class Soldado extends Object { ... }`.

Esto tiene consecuencias prácticas importantes: todos los objetos en Java poseen los métodos definidos en `Object`, como `toString()` (representación textual del objeto), `equals(Object o)` (comparación de igualdad), `hashCode()` (código hash para estructuras de datos) y `getClass()` (información sobre la clase en tiempo de ejecución), entre otros. Estos métodos pueden (y en muchos casos deben) ser sobrescritos en las subclases para proporcionar un comportamiento adecuado.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La **herencia múltiple** es la capacidad de una clase de heredar de **más de una superclase** al mismo tiempo. Por ejemplo, si se tuviera una clase `VehículoAnfibio` que heredara tanto de `Coche` como de `Barco`, eso sería herencia múltiple. Este mecanismo existe en lenguajes como C++ y Python.

En **Java no existe herencia múltiple de clases**. Una clase solo puede extender (`extends`) a una única superclase. Esta decisión de diseño se tomó para evitar los problemas asociados a la herencia múltiple, en particular el llamado **problema del diamante**: si dos superclases heredan de una misma clase base y ambas sobrescriben un método, la subclase que hereda de ambas no sabría cuál de las dos versiones del método utilizar.

No obstante, Java ofrece una alternativa parcial a través de las **interfaces**. Una clase puede implementar (`implements`) múltiples interfaces, lo que permite que un objeto tenga múltiples "tipos" sin los problemas de la herencia múltiple de implementación. Desde Java 8, las interfaces pueden incluir métodos con implementación por defecto (`default`), lo que acerca aún más este mecanismo a una forma controlada de herencia múltiple.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta

Las excepciones en Java son objetos, y al ser objetos, se pueden crear excepciones personalizadas mediante herencia. Para crear una excepción **no controlada** (unchecked), se debe extender de `RuntimeException` (o de alguna subclase suya). Las excepciones no controladas no obligan al programador a capturarlas ni a declararlas en la firma del método con `throws`.

En el siguiente ejemplo se crea `UsuarioNoEncontradoException`, que almacena por composición el objeto `Usuario` que provocó el problema. Se proporcionan dos constructores: uno básico con mensaje y usuario, y otro sobrecargado que además permite incluir la **causa subyacente** (otra excepción que originó el problema), siguiendo el patrón de encadenamiento de excepciones de Java.

```java
public class Usuario {
    private String dni;
    private String nombre;

    public Usuario(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }

    @Override
    public String toString() {
        return "Usuario{" + "dni='" + dni + "', nombre='" + nombre + "'}";
    }
}

public class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuario;

    // Constructor con mensaje y usuario
    public UsuarioNoEncontradoException(String message, Usuario usuario) {
        super(message);
        this.usuario = usuario;
    }

    // Constructor sobrecargado que además permite indicar la causa
    public UsuarioNoEncontradoException(String message, Usuario usuario, Throwable cause) {
        super(message, cause);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}
```

De esta forma, al capturar la excepción se puede obtener el `Usuario` concreto que dio el problema mediante `getUsuario()`, y si se proporcionó una causa, se puede acceder a ella con `getCause()`. Esto facilita enormemente la depuración y el manejo de errores en aplicaciones complejas.


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

No se debe emplear herencia simplemente para reutilizar código porque la herencia establece una relación semántica muy fuerte entre las clases: **"A es-un B"**. Si se hereda de una clase solo porque tiene métodos útiles, sin que exista realmente esa relación "es-un", se crea un diseño incorrecto y frágil. Por ejemplo, si una clase `Pila` heredase de `ArrayList` solo para reutilizar su almacenamiento interno, se estaría diciendo que "una Pila es-un ArrayList", lo que expondría métodos como `get(i)` o `add(index, elem)` que no tienen sentido en una pila.

La herencia es la relación más fuerte que se puede establecer entre dos clases. Cuando se hereda, la subclase queda acoplada a la superclase: cualquier cambio en la superclase puede afectar a la subclase. Si la única motivación es reutilizar código, la **composición** es una alternativa mucho más segura: se crea un atributo del tipo que se quiere reutilizar y se delegan las llamadas necesarias. Así se obtiene la reutilización sin el acoplamiento ni la exposición innecesaria de interfaz.

En resumen, la herencia debe usarse solo cuando la relación "es-un" es genuina y tiene sentido a nivel de modelo del dominio. Para la simple reutilización de código, la composición ("tiene-un") es la opción preferida.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

Se debe favorecer la composición frente a la herencia por varias razones fundamentales. La composición proporciona **mayor flexibilidad**: la relación entre los componentes se puede cambiar en tiempo de ejecución (por ejemplo, inyectando distintas implementaciones), mientras que la herencia es una relación estática definida en tiempo de compilación que no se puede modificar después.

Además, la composición produce un **menor acoplamiento** entre las clases. Con herencia, la subclase depende íntimamente de los detalles de implementación de la superclase. Si la superclase cambia su implementación interna, la subclase puede romperse aunque su interfaz pública no haya cambiado. Con composición, la clase solo depende de la interfaz pública del componente que utiliza, lo que hace el sistema más robusto ante cambios.

Por último, la composición permite **combinar comportamientos de múltiples fuentes** sin los problemas de la herencia múltiple. Una clase puede tener varios atributos de distintos tipos y delegar en cada uno según el caso. Esto resulta en diseños más modulares, más fáciles de testear y más fáciles de mantener a largo plazo.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

Cuando se dice que la herencia **"rompe la encapsulación"**, se refiere a que la subclase necesita conocer detalles internos de la superclase para funcionar correctamente. La encapsulación consiste en ocultar la implementación interna y exponer solo una interfaz pública. Con la herencia, esta barrera se debilita: la subclase puede acceder a miembros `protected`, sobrescribir métodos que son invocados internamente por la superclase, y en general depende de cómo la superclase implementa sus operaciones.

Un ejemplo clásico de este problema se da cuando la superclase llama internamente a uno de sus propios métodos que la subclase ha sobrescrito. Si la superclase cambia su implementación interna (por ejemplo, deja de usar ese método internamente), el comportamiento de la subclase puede cambiar o romperse sin que nadie haya modificado la subclase. Esto viola el principio de encapsulación porque la correcta funcionamiento de la subclase depende de detalles de implementación que no forman parte del contrato público de la superclase.

Con la composición, en cambio, la clase cliente solo interactúa con la interfaz pública del componente, sin depender de sus detalles internos. Por eso se dice que la composición respeta mejor la encapsulación: los cambios internos del componente no afectan al código que lo utiliza, siempre que la interfaz pública se mantenga.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

A continuación se muestran las dos alternativas para modelar la situación de `Estudiante` y `Trabajador` con datos personales comunes (DNI y nombre).

**Opción 1: Herencia con superclase `Persona`**

```java
public class Persona {
    private String dni;
    private String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
}

public class Estudiante extends Persona {
    private String carrera;

    public Estudiante(String dni, String nombre, String carrera) {
        super(dni, nombre);
        this.carrera = carrera;
    }

    public String getCarrera() { return carrera; }
}

public class Trabajador extends Persona {
    private String empresa;

    public Trabajador(String dni, String nombre, String empresa) {
        super(dni, nombre);
        this.empresa = empresa;
    }

    public String getEmpresa() { return empresa; }
}
```

**Opción 2: Composición con clase `DatosPersonales`**

```java
public class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
}

public class Estudiante {
    private DatosPersonales datosPersonales;
    private String carrera;

    public Estudiante(DatosPersonales datosPersonales, String carrera) {
        this.datosPersonales = datosPersonales;
        this.carrera = carrera;
    }

    public DatosPersonales getDatosPersonales() { return datosPersonales; }
    public String getCarrera() { return carrera; }
}

public class Trabajador {
    private DatosPersonales datosPersonales;
    private String empresa;

    public Trabajador(DatosPersonales datosPersonales, String empresa) {
        this.datosPersonales = datosPersonales;
        this.empresa = empresa;
    }

    public DatosPersonales getDatosPersonales() { return datosPersonales; }
    public String getEmpresa() { return empresa; }
}
```

En la opción de herencia, `Estudiante` y `Trabajador` **son una** `Persona`, con lo que heredan directamente DNI y nombre. En la opción de composición, ambos **tienen** `DatosPersonales`, que se recibe como parámetro en el constructor. La segunda opción es más flexible: si en el futuro alguien es `Estudiante` y `Trabajador` a la vez, con composición ambos pueden compartir la misma instancia de `DatosPersonales`, mientras que con herencia no se podría heredar de las dos clases simultáneamente (Java no permite herencia múltiple).
