<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

En C, la composición se consigue incluyendo una `struct` como campo dentro de otra `struct`. En este caso, un `Punto` contiene dos coordenadas (`x` e `y`), y una `Linea` se compone de dos `Punto`. Esta relación se describe como "una línea **tiene** dos puntos", que es la esencia de la composición: construir estructuras complejas a partir de otras más simples.

A continuación se muestra el ejemplo completo con las funciones para calcular la distancia entre puntos y la longitud de una línea:

```c
#include <stdio.h>
#include <math.h>

typedef struct {
    double x;
    double y;
} Punto;

typedef struct {
    Punto inicio;
    Punto fin;
} Linea;

double distancia(Punto a, Punto b) {
    double dx = b.x - a.x;
    double dy = b.y - a.y;
    return sqrt(dx * dx + dy * dy);
}

double longitud(Linea l) {
    return distancia(l.inicio, l.fin);
}

int main() {
    Punto p1 = {0.0, 0.0};
    Punto p2 = {3.0, 4.0};
    Linea linea = {p1, p2};

    printf("Distancia entre puntos: %f\n", distancia(p1, p2));
    printf("Longitud de la línea: %f\n", longitud(linea));
    return 0;
}
```

Nótese que en C no hay forma de proteger los campos internos: cualquier parte del código puede acceder y modificar `linea.inicio.x` directamente. Esto contrasta con la orientación a objetos, donde la encapsulación permite ocultar los detalles internos.


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.

### Respuesta

En Java, la composición se implementa declarando atributos de una clase que son instancias de otra clase. Para garantizar la inmutabilidad, se declaran los atributos como `private final` y no se proporcionan métodos que permitan modificarlos (*setters*). De este modo, tanto `Punto` como `Linea` son objetos que, una vez creados, no pueden cambiar su estado.

```java
public class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() {
        return x;
    }

    public double getY() {
        return y;
    }

    public double distancia(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

```java
public class Linea {
    private final Punto inicio;
    private final Punto fin;

    public Linea(Punto inicio, Punto fin) {
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }

    public Punto getInicio() {
        return inicio;
    }

    public Punto getFin() {
        return fin;
    }
}
```

La ventaja frente a C es clara: nadie puede modificar las coordenadas de un punto ni cambiar los puntos que componen una línea tras su creación. En C, cualquier parte del código podría hacer `linea.inicio.x = 99.0` sin restricciones. En Java, al declarar los atributos como `private final` y al ser `Punto` inmutable, se garantiza la integridad del objeto `Linea` durante toda su vida. Esto es un ejemplo directo de cómo la encapsulación en orientación a objetos mejora la seguridad y robustez del diseño.


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La **multiplicidad** en la composición indica cuántas instancias de una clase están asociadas a una instancia de otra clase. Es un concepto que describe "cuántos" objetos participan en cada lado de la relación. Se expresa típicamente con valores como `1`, `0..1`, `0..*` (cero o muchos), `1..*` (uno o muchos), o un número concreto como `2`.

En el ejemplo de `Linea` y `Punto`:

- **De `Linea` a `Punto`**: la multiplicidad es **2**, ya que cada línea está compuesta por exactamente dos puntos (el punto de inicio y el punto de fin).
- **De `Punto` a `Linea`**: la multiplicidad es **0..*** (cero o muchos), ya que un punto puede no pertenecer a ninguna línea, o puede ser utilizado como extremo de una o varias líneas.

Es importante señalar que la multiplicidad se analiza desde ambas direcciones. En este caso, la relación no es simétrica: una línea siempre necesita exactamente dos puntos, pero un punto es independiente y puede existir sin formar parte de ninguna línea.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

La **composición fuerte** implica que los objetos contenidos (las "partes") no tienen sentido ni existencia fuera del objeto contenedor (el "todo"). El ciclo de vida de las partes está completamente ligado al del contenedor: cuando el contenedor se crea, se crean las partes; cuando se destruye, las partes se destruyen con él. Las partes no se comparten entre varios contenedores. A esta relación se la conoce habitualmente como **"composición"** propiamente dicha. Un ejemplo clásico: las habitaciones de una casa. Si se derriba la casa, las habitaciones dejan de existir.

La **composición débil** implica que los objetos contenidos tienen una existencia independiente del contenedor. El contenedor referencia a las partes, pero no es dueño de su ciclo de vida: las partes pueden existir antes de que el contenedor se cree y seguir existiendo después de que se destruya. Además, las partes pueden ser compartidas entre varios contenedores. A esta relación se la conoce como **"asociación"** o **"agregación"**. Un ejemplo clásico: un departamento universitario y sus profesores. Si se elimina el departamento, los profesores siguen existiendo.

La consecuencia principal radica en la responsabilidad sobre la creación y destrucción de los objetos: en la composición fuerte, el contenedor es responsable; en la débil, no lo es.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

En ese caso se habla de **dependencia**, no de composición. La dependencia es la relación más débil entre clases y se produce cuando una clase "usa" a otra de forma temporal, sin almacenarla como atributo. Esto ocurre cuando una clase recibe un objeto de otra como parámetro de un método, lo devuelve como valor de retorno, lo crea con `new` dentro de un método como variable local, o lo usa como variable local dentro de un bloque de código.

La diferencia fundamental con la composición es que en la dependencia no existe una relación "tiene-un" permanente. El objeto usado solo existe durante la ejecución del método y no forma parte del estado del objeto que lo utiliza. Por ejemplo, si un método de `Calculadora` recibe un `Punto` como parámetro para realizar un cálculo, `Calculadora` *depende* de `Punto`, pero no lo *tiene* como parte de su estructura.

En un diagrama UML, la dependencia se representa con una flecha discontinua, mientras que la composición (fuerte o débil) se representa con una flecha con rombo. La dependencia indica un acoplamiento mínimo: un cambio en la clase usada podría afectar a la clase que la usa, pero la relación es transitoria y no estructural.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

En la **composición fuerte**, los puntos se crean dentro de la propia `Linea`. El constructor recibe las coordenadas (datos primitivos) y es `Linea` quien crea los objetos `Punto`. De esta forma, los puntos no existen fuera de la línea, y su ciclo de vida está completamente ligado al de ella:

```java
public class LineaFuerte {
    private final Punto inicio;
    private final Punto fin;

    public LineaFuerte(double x1, double y1, double x2, double y2) {
        // Linea crea sus propios puntos: composición fuerte
        this.inicio = new Punto(x1, y1);
        this.fin = new Punto(x2, y2);
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
```

En la **composición débil** (agregación), los puntos se crean fuera de `Linea` y se le pasan como parámetros. La línea simplemente almacena referencias a puntos que ya existen de forma independiente, y que podrían ser compartidos por otras líneas u otros objetos:

```java
public class LineaDebil {
    private final Punto inicio;
    private final Punto fin;

    public LineaDebil(Punto inicio, Punto fin) {
        // Linea recibe puntos creados externamente: composición débil
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
```

La diferencia clave está en **quién crea los objetos**. En la composición fuerte, el contenedor (la línea) es responsable de crear sus partes. En la composición débil, las partes existen independientemente y el contenedor solo las referencia. En ambos casos, el código Java resultante es muy similar, pero la semántica de la relación es diferente.


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En Java, a diferencia de lenguajes como C++ donde existe un destructor explícito y el programador debe liberar la memoria manualmente, **no es necesario destruir objetos de forma explícita**. Java cuenta con un **recolector de basura** (*garbage collector*) que se encarga automáticamente de liberar la memoria de los objetos que ya no son referenciados por ninguna parte del programa.

En el caso de la composición fuerte entre `Linea` y `Punto`: cuando un objeto `Linea` deja de ser referenciado (por ejemplo, porque la variable que lo contenía sale de ámbito o se le asigna `null`), ya nadie tiene acceso a los `Punto` que fueron creados internamente por esa línea (asumiendo que no se han expuesto al exterior). En ese momento, tanto la `Linea` como sus `Punto` se convierten en candidatos para la recolección de basura.

Por tanto, no se observa que `Linea` destruya explícitamente los `Punto` porque en Java no existe la necesidad de hacerlo. El recolector de basura detecta automáticamente que esos objetos son inalcanzables y los elimina en algún momento posterior. Esto simplifica enormemente la gestión de memoria comparado con C/C++, donde olvidar liberar memoria provoca *memory leaks* (fugas de memoria).


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

A continuación se presenta la clase `Profesor` con los atributos básicos necesarios:

```java
public class Profesor {
    private final String nombre;

    public Profesor(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}
```

Y la clase `Departamento`, que implementa las dos composiciones (con la lista de profesores y con el director), utilizando un array primitivo y manteniendo la invariante de que el director siempre forme parte de la lista:

```java
public class Departamento {
    private static final int MAX_PROFESORES = 50;
    private final String nombre;
    private Profesor[] profesores;
    private int numProfesores;
    private Profesor director;

    public Departamento(String nombre, Profesor director) {
        if (director == null) {
            throw new IllegalArgumentException("El departamento debe tener un director desde el inicio.");
        }
        this.nombre = nombre;
        this.profesores = new Profesor[MAX_PROFESORES];
        this.numProfesores = 0;
        this.director = director;
        addProfesor(director); // El director forma parte de la lista de profesores
    }

    public void addProfesor(Profesor profesor) {
        if (profesor == null) {
            throw new IllegalArgumentException("El profesor no puede ser null.");
        }
        if (numProfesores >= MAX_PROFESORES) {
            throw new IllegalStateException("El departamento ha alcanzado el máximo de profesores.");
        }
        profesores[numProfesores] = profesor;
        numProfesores++;
    }

    public void removeProfesor(int posicion) {
        if (posicion < 0 || posicion >= numProfesores) {
            throw new IndexOutOfBoundsException("Posición inválida: " + posicion);
        }
        if (profesores[posicion] == director) {
            throw new IllegalStateException(
                "No se puede eliminar al director. Cambie el director antes de eliminarlo."
            );
        }
        // Desplazar los elementos hacia la izquierda para rellenar el hueco
        for (int i = posicion; i < numProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[numProfesores - 1] = null;
        numProfesores--;
    }

    public int getNumProfesores() {
        return numProfesores;
    }

    public Profesor getProfesor(int posicion) {
        if (posicion < 0 || posicion >= numProfesores) {
            throw new IndexOutOfBoundsException("Posición inválida: " + posicion);
        }
        return profesores[posicion];
    }

    public Profesor getDirector() {
        return director;
    }

    public void setDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El director no puede ser null.");
        }
        // Verificar que el nuevo director forme parte de la lista de profesores
        boolean encontrado = false;
        for (int i = 0; i < numProfesores; i++) {
            if (profesores[i] == nuevoDirector) {
                encontrado = true;
                break;
            }
        }
        if (!encontrado) {
            throw new IllegalArgumentException(
                "El nuevo director debe ser un profesor del departamento."
            );
        }
        this.director = nuevoDirector;
    }

    public String getNombre() {
        return nombre;
    }
}
```

Se observan varios puntos clave del diseño. Primero, la encapsulación se mantiene: el array `profesores` es `private` y no se expone al exterior; el acceso se realiza mediante `getNumProfesores()` y `getProfesor(int)`. Segundo, la invariante de clase (el director siempre pertenece a la lista de profesores) se protege en dos puntos: al cambiar el director con `setDirector`, se verifica que ya esté en la lista; y al eliminar un profesor con `removeProfesor`, se impide eliminar al director actual. Tercero, se lanzan excepciones cuando se violan las precondiciones (profesor `null`, posición inválida, capacidad máxima alcanzada, director no perteneciente al departamento), lo que garantiza que el objeto siempre se encuentre en un estado consistente.


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

A continuación se muestra la clase `Departamento` utilizando `List<Profesor>` en lugar de arrays primitivos:

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Departamento {
    private final String nombre;
    private final List<Profesor> profesores;
    private Profesor director;

    public Departamento(String nombre, Profesor director) {
        if (director == null) {
            throw new IllegalArgumentException("El departamento debe tener un director desde el inicio.");
        }
        this.nombre = nombre;
        this.profesores = new ArrayList<>();
        this.director = director;
        addProfesor(director);
    }

    public void addProfesor(Profesor profesor) {
        if (profesor == null) {
            throw new IllegalArgumentException("El profesor no puede ser null.");
        }
        profesores.add(profesor);
    }

    public void removeProfesor(int posicion) {
        if (posicion < 0 || posicion >= profesores.size()) {
            throw new IndexOutOfBoundsException("Posición inválida: " + posicion);
        }
        if (profesores.get(posicion) == director) {
            throw new IllegalStateException(
                "No se puede eliminar al director. Cambie el director antes de eliminarlo."
            );
        }
        profesores.remove(posicion);
    }

    public int getNumProfesores() {
        return profesores.size();
    }

    public Profesor getProfesor(int posicion) {
        if (posicion < 0 || posicion >= profesores.size()) {
            throw new IndexOutOfBoundsException("Posición inválida: " + posicion);
        }
        return profesores.get(posicion);
    }

    public List<Profesor> getProfesores() {
        return Collections.unmodifiableList(profesores);
    }

    public Profesor getDirector() {
        return director;
    }

    public void setDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El director no puede ser null.");
        }
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalArgumentException(
                "El nuevo director debe ser un profesor del departamento."
            );
        }
        this.director = nuevoDirector;
    }

    public String getNombre() {
        return nombre;
    }
}
```

El código que se ha ahorrado con `List` respecto al array primitivo es significativo: ya no es necesario mantener un contador manual (`numProfesores`), ni gestionar el desplazamiento de elementos al eliminar (la `List` lo hace internamente con `remove(int)`), ni definir una constante de tamaño máximo (`MAX_PROFESORES`), ni recorrer el array buscando un elemento manualmente (se usa `contains()`). El código queda más limpio y menos propenso a errores.

Respecto al método `getProfesores()`: si se devolviera directamente la lista interna (`return profesores`), se estaría **rompiendo la encapsulación**. El código externo podría modificar la lista (añadir, eliminar o reordenar profesores) sin pasar por los métodos del departamento, saltándose las validaciones y violando potencialmente la invariante de clase (por ejemplo, eliminando al director de la lista). La solución es devolver una **vista no modificable** de la lista mediante `Collections.unmodifiableList(profesores)`. De esta forma, el llamador puede consultar la lista pero no puede modificarla; cualquier intento de añadir o eliminar elementos lanzará una `UnsupportedOperationException`.


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

Una composición recursiva se produce cuando una clase tiene un atributo cuyo tipo es la propia clase. En el caso de `Persona`, cada persona tiene una madre que también es una `Persona`, la cual a su vez tiene su propia madre, y así sucesivamente. La cadena se termina con `null` cuando se desconoce o no se registra la madre.

```java
public class Persona {
    private final String nombre;
    private final Persona madre;

    public Persona(String nombre, Persona madre) {
        this.nombre = nombre;
        this.madre = madre;
    }

    public String getNombre() {
        return nombre;
    }

    public Persona getMadre() {
        return madre;
    }

    public static void main(String[] args) {
        Persona abuela = new Persona("María", null);
        Persona madre = new Persona("Ana", abuela);
        Persona nieto = new Persona("Carlos", madre);

        // Recorrer la cadena de madres
        Persona actual = nieto;
        while (actual != null) {
            System.out.println(actual.getNombre());
            actual = actual.getMadre();
        }
        // Salida:
        // Carlos
        // Ana
        // María
    }
}
```

La clase `Persona` es inmutable porque sus atributos son `private final` y no se proporcionan métodos para modificarlos. Nótese cómo la construcción de la familia se hace desde la persona más antigua (la abuela, cuya madre es `null`) hacia la más reciente (el nieto), ya que para crear una persona se necesita que su madre ya exista.

Otros ejemplos clásicos de composiciones recursivas son: las **listas enlazadas**, donde cada nodo contiene una referencia al siguiente nodo (que es del mismo tipo); los **árboles**, donde cada nodo tiene hijos que son también nodos; los **sistemas de ficheros**, donde una carpeta puede contener otras carpetas; y, como menciona el propio enunciado, las **excepciones en Java**, donde cada excepción puede tener una causa que también es una excepción. Este patrón es muy frecuente para representar estructuras jerárquicas o encadenadas.


## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Una relación de composición **bidireccional** es aquella en la que ambas partes de la relación se conocen mutuamente. Es decir, no solo el contenedor tiene una referencia a las partes, sino que cada parte también tiene una referencia de vuelta hacia su contenedor. En el ejemplo de `Departamento` y `Profesor`, la relación actual es unidireccional: el departamento conoce a sus profesores, pero un profesor no sabe a qué departamento pertenece.

Para implementar la bidireccionalidad, habría que añadir un atributo `departamento` en la clase `Profesor` que almacene una referencia al departamento al que pertenece. Se debe tener cuidado de mantener la **consistencia** de ambos lados de la relación: cuando se añade un profesor a un departamento, hay que actualizar tanto la lista del departamento como el atributo del profesor, y lo mismo al eliminarlo. Esto se haría así:

```java
public class Profesor {
    private final String nombre;
    private Departamento departamento;

    public Profesor(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }

    public Departamento getDepartamento() {
        return departamento;
    }

    // Acceso de paquete: solo Departamento debería llamar a este método
    void setDepartamento(Departamento departamento) {
        this.departamento = departamento;
    }
}
```

En la clase `Departamento`, los métodos `addProfesor` y `removeProfesor` deberían actualizarse para mantener la sincronización:

```java
public void addProfesor(Profesor profesor) {
    if (profesor == null) {
        throw new IllegalArgumentException("El profesor no puede ser null.");
    }
    profesores.add(profesor);
    profesor.setDepartamento(this); // Mantener la consistencia bidireccional
}

public void removeProfesor(int posicion) {
    if (posicion < 0 || posicion >= profesores.size()) {
        throw new IndexOutOfBoundsException("Posición inválida: " + posicion);
    }
    if (profesores.get(posicion) == director) {
        throw new IllegalStateException(
            "No se puede eliminar al director. Cambie el director antes de eliminarlo."
        );
    }
    Profesor eliminado = profesores.remove(posicion);
    eliminado.setDepartamento(null); // Mantener la consistencia bidireccional
}
```

El principal desafío de las relaciones bidireccionales es garantizar que ambos lados estén siempre sincronizados. Si se modifica un lado sin actualizar el otro, el modelo queda en un estado inconsistente. Por eso es habitual que la responsabilidad de mantener la relación recaiga en un solo lado (típicamente el contenedor, `Departamento` en este caso), y que el método `setDepartamento` del `Profesor` tenga visibilidad restringida (de paquete, en este ejemplo) para evitar que código externo lo llame directamente y rompa la consistencia.

---

## Resumen de preguntas

| Pregunta | Tema |
|----------|------|
| **1** | Composición en C con `struct`: línea de puntos, distancia y longitud |
| **2** | Composición en Java: `Punto` y `Linea` inmutables con encapsulación |
| **3** | Multiplicidad entre `Linea` y `Punto` en ambas direcciones |
| **4** | Composición fuerte vs. débil, ciclo de vida, "asociación" vs. "composición" |
| **5** | Dependencia: uso temporal sin relación "tiene-un" permanente |
| **6** | Implementación de composición fuerte y débil entre `Linea` y `Punto` |
| **7** | Destrucción de objetos en Java: el recolector de basura |
| **8** | `Departamento` y `Profesor` con array primitivo, invariante del director |
| **9** | Uso de `List` en lugar de arrays + encapsulación con `Collections.unmodifiableList` |
| **10** | Composiciones recursivas: `Persona` con madre + ejemplos clásicos |
| **11** | Relaciones bidireccionales y sincronización de ambos lados |
