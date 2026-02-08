<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

### Respuesta

La **encapsulación** en Programación Orientada a Objetos es el mecanismo que agrupa datos (atributos) y los métodos que operan sobre esos datos en una única unidad o "caja negra" (la clase). Por otro lado, la **ocultación de información** es el principio de diseño que restringe el acceso directo a algunos de los componentes del objeto. En C, esto sería comparable a tener un `struct` donde los datos son internos y solo se pueden modificar a través de funciones específicas definidas en una interfaz, aunque en C el compilador no impone estas restricciones de acceso tan fuertemente como en Java o C++. Se busca que el estado interno del objeto esté protegido de interferencias y usos incorrectos desde el exterior.

Entre las ventajas de la ocultación de información se encuentra el **desacoplamiento**: los detalles internos de una clase pueden cambiar sin afectar a las partes del programa que la utilizan, siempre que la interfaz pública se mantenga. Esto facilita enormemente el mantenimiento y la evolución del software. También mejora la **integridad de los datos**, ya que se puede asegurar que el objeto siempre se encuentre en un estado válido, validando cualquier intento de modificación. Finalmente, reduce la complejidad cognitiva para el programador, que solo necesita entender qué hace la clase (su interfaz) y no cómo lo hace internamente.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

### Respuesta

La **interfaz pública** de una clase se refiere al conjunto de métodos y atributos que son accesibles desde fuera de la clase. Es el "contrato" que la clase ofrece al mundo exterior; define qué operaciones se pueden realizar con sus objetos y cómo interactuar con ellos. Haciendo una analogía con C, sería similar a las declaraciones de funciones y tipos en un archivo de cabecera (`.h`) que se entregan a otro desarrollador para que use tu librería, ocultando la implementación concreta que reside en el `.c`.

La relación con la ocultación de información es directa: todo aquello que *no* forma parte de la interfaz pública se considera detalle de implementación y debe estar oculto (ser privado). Cuanto más pequeña y bien definida sea la interfaz pública, más libertad tendrá el desarrollador para cambiar la implementación interna (la parte oculta) sin romper el código de quienes usan la clase. La ocultación garantiza que solo se exponga lo estrictamente necesario.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

### Respuesta

Es crucial diseñar la interfaz pública con sumo cuidado porque, una vez que otros módulos o programadores empiezan a utilizarla, cualquier cambio en ella puede provocar errores en cascada en todo el sistema. Si se altera la firma de un método público o se elimina, todo el código que dependía de él dejará de compilar o funcionar correctamente. Es un compromiso a largo plazo difícil de romper sin costes elevados de refactorización.

Por el contrario, cambiar la implementación interna (lo que está oculto) es fácil y seguro, siempre que se respete el contrato de la interfaz pública. Por ello, se recomienda exponer el mínimo número de métodos y atributos posibles (principio del mínimo privilegio/exposición), manteniendo la mayoría de los detalles como privados. Esto preserva la flexibilidad para mejorar, optimizar o corregir la clase en el futuro sin afectar a sus clientes.


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

### Respuesta

Las **invariantes de clase** son condiciones lógicas que deben ser siempre ciertas para cualquier objeto de esa clase durante toda su vida útil (excepto quizás momentáneamente durante la ejecución de un método que está modificando el estado). Por ejemplo, en una clase que represente un intervalo de tiempo, una invariante podría ser que la "hora de fin" nunca sea anterior a la "hora de inicio". Son las reglas de consistencia interna del objeto.

La ocultación de información es fundamental para mantener estas invariantes. Si los atributos fueran públicos (acceso directo como en un `struct` clásico de C), cualquier parte del código podría asignar valores arbitrarios que violaran las reglas (por ejemplo, poner la hora de fin antes de la de inicio), dejando al objeto en un estado inválido o corrupto. Al hacer los atributos privados y obligar a pasar por métodos públicos (setters o métodos de negocio) para modificarlos, la clase puede verificar que las invariantes se cumplen antes de aceptar cualquier cambio.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

### Respuesta

A continuación se presenta un ejemplo de clase `Punto` que encapsula sus coordenadas `x` e `y`. La palabra clave `private` indica que esos datos solo son accesibles desde dentro de la propia clase (ocultación), mientras que `public` indica que el constructor y el método `calcularDistanciaAOrigen` pueden ser invocados desde cualquier parte del programa.

```java
public class Punto {
    // Atributos privados: Ocultos al exterior
    private double x;
    private double y;

    // Constructor público: Parte de la interfaz pública
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Método público: Parte de la interfaz pública
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
}
```

La **interfaz pública** de esta clase está formada por el constructor `Punto(double, double)` y el método `calcularDistanciaAOrigen()`. Los atributos `x` e `y` no forman parte de la interfaz pública; son detalles de implementación. Un usuario de la clase no necesita saber si las coordenadas se guardan como cartesianas o polares internamente, solo necesita saber cómo crear un punto y pedirle su distancia. Esto permite que el estado interno esté protegido.


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

### Respuesta

En Java, los modificadores de acceso `public` y `private` se pueden aplicar a **miembros de una clase** (atributos y métodos) y a **constructores**. Esto permite controlar granularmente quién puede ver o usar cada parte de la definición de un objeto. Por ejemplo, se pueden tener atributos privados, pero métodos públicos, o incluso constructores privados para restringir la creación de instancias.

También se pueden aplicar a las propias **clases** (y a interfaces), pero con una restricción: una clase de nivel superior (no anidada) solo puede ser `public` o no tener modificador (acceso de paquete). No tiene sentido declarar una clase externa como `private` porque nadie podría usarla. Sin embargo, las clases internas (definidas dentro de otra clase) sí pueden ser marcadas como `private`, limitando su uso exclusivamente a la clase que las contiene.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

### Respuesta

Además de `public` (+) y `private` (-), en POO comúnmente existe la visibilidad **protegida** (`protected`, #), que permite el acceso a las subclases (herencia). En Java, específicamente, existen cuatro niveles de acceso:
1.  **Public**: Accesible desde cualquier lugar.
2.  **Private**: Accesible solo desde la propia clase.
3.  **Protected**: Accesible desde el mismo paquete y desde subclases (aunque estén en otro paquete).
4.  **Default (o Package-Private)**: Es la ausencia de modificador. Accesible solo desde clases del mismo paquete (similar a "friend" en C++ pero a nivel de directorio/paquete).

En otros lenguajes como C++, los conceptos son similares (`public`, `private`, `protected`), pero la semántica de la herencia también afecta a la visibilidad (herencia pública, privada o protegida), algo que en Java no ocurre. Además, C++ tiene el concepto de `friend` que permite excepciones a la encapsulación, mientras que Java usa la estructura de paquetes para agrupar clases relacionadas con un nivel de confianza mutua.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

### Respuesta

La respuesta correcta es **(a) otras clases**. Los miembros privados están ocultos para otras clases, pero **NO** para otras instancias de la misma clase. La privacidad en Java (y en C++) es un concepto a nivel de **clase**, no a nivel de **objeto**. Esto significa que un objeto de la clase `Punto` puede acceder a los miembros privados de *otro* objeto de la clase `Punto`.

```java
public class Punto {
    private double x, y;

    // ... constructor ...

    public double calcularDistanciaAPunto(Punto otro) {
        // Podemos acceder directamente a los atributos privados 'x' e 'y' de 'otro'
        // porque 'otro' es de la misma clase que 'this'.
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Esto es muy útil para implementar métodos binarios como comparaciones, copias o cálculos de distancia, ya que permite manipular directamente la estructura interna de ambos operandos de manera eficiente sin tener que exponer getters públicos.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

### Respuesta

Los métodos "getter" (accesores) y "setter" (mutadores) son convenciones en POO para leer y escribir el valor de un atributo privado, respectivamente. En lugar de exponer el atributo directamente (`objeto.dato`), se invoca un método (`objeto.getDato()` o `objeto.setDato(valor)`). Son los guardianes de la puerta de los datos.

Su propósito principal es mantener el control sobre el acceso al estado del objeto. Un "getter" permite leer el valor sin permitir modificarlo (lectura sola), o puede devolver una copia calculada. Un "setter" permite validar el nuevo valor antes de asignarlo (por ejemplo, asegurar que una edad no sea negativa) para proteger las invariantes de la clase. Si el acceso fuera directo, no habría forma de impedir una asignación inválida.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

### Respuesta

No, en el contexto de encapsulación y diseño de software, cuando decimos que mejora la "seguridad" (**safety**), no nos referimos a seguridad informática contra hackers (security) o encriptación. Nos referimos a la **robustez** y **fiabilidad** del programa frente a errores de programación y uso incorrecto.

"Seguro" aquí significa que el objeto se protege a sí mismo de entrar en estados inconsistentes o inválidos debido a errores lógicos en otras partes del código. Al ocultar la implementación y forzar el uso de una interfaz bien definida, prevenimos "bugs" que podrían corromper los datos. Es seguridad contra el uso accidental o descuidado, garantizando que el software se comporte de manera predecible y correcta.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

### Respuesta

Un **miembro de instancia** pertenece a un objeto concreto; cada objeto tiene su propia copia de ese atributo. Modificarlo en un objeto no afecta a los otros. Por contra, un **miembro de clase** (declarado con `static` en Java) es compartido por todas las instancias de esa clase; solo existe una copia en memoria para toda la aplicación. Es similar a una variable global pero contenida dentro del espacio de nombres de la clase.

Sí, los miembros de clase también se pueden ocultar (ser privados). De hecho, es muy común tener constantes privadas o métodos estáticos de ayuda que son internos a la clase y no deben ser accesibles desde fuera. La visibilidad (`private`/`public`) y el ámbito (`static`/instancia) son conceptos ortogonales.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

### Respuesta

Sí, tiene mucho sentido y es una práctica común en ciertos patrones de diseño. Un constructor privado impide que se puedan crear instancias de la clase desde fuera de la misma usando el operador `new`.

Esto es útil para:
1.  **Clases de utilidad**: Clases que solo contienen métodos estáticos (como `java.lang.Math`) y de las que no tiene sentido crear objetos.
2.  **Patrón Singleton**: Donde se quiere garantizar que solo exista una única instancia de la clase en todo el programa.
3.  **Control de creación**: Para obligar a usar métodos factoría estáticos que pueden realizar validaciones adicionales o retornar objetos cacheados en lugar de crear uno nuevo siempre.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

### Respuesta

En Java, los miembros de clase se indican con la palabra clave `static`. Aquí se muestra cómo añadir miembros de clase para rastrear los máximos valores de `x` e `y` históricos establecidos en cualquier punto creado.

```java
public class Punto {
    private double x, y;
    
    // Miembros de CLASE (static): Compartidos por todos los puntos
    // Se inicializan a valores muy bajos
    private static double xMax = Double.NEGATIVE_INFINITY;
    private static double yMax = Double.NEGATIVE_INFINITY;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        // Actualizamos los máximos de clase si este nuevo punto los supera
        // Note el uso del nombre de la clase para acceder, aunque no es estricto
        if (x > Punto.xMax) Punto.xMax = x;
        if (y > Punto.yMax) Punto.yMax = y;
    }
    
    // Métodos estáticos para consultar los valores de clase
    public static double getXMax() { return xMax; }
    public static double getYMax() { return yMax; }

    // ... resto de métodos ...
}
```


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

### Respuesta

Un método factoría es un método estático que devuelve una instancia de la clase. Al ser estático, no necesita ser invocado sobre un objeto existente, sino sobre la clase misma.

```java
    // Método factoría estático
    public static Punto crearPuntoRedondeado(double x, double y) {
        double xRedondeada = Math.round(x);
        double yRedondeada = Math.round(y);
        // Llamamos al constructor privado o público desde aquí
        return new Punto(xRedondeada, yRedondeada);
    }
```

Sí, he usado `static` porque la creación del punto no depende de ningún estado previo de otro objeto `Punto`. Es una función que está asociada a la clase `Punto` conceptualmente, pero que opera como un generador independiente.


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

### Respuesta

Aquí se demuestra la potencia de la encapsulación: podemos cambiar radicalmente cómo guardamos los datos internamente (usando un array en lugar de variables sueltas) sin que cambie la forma en que se usa la clase desde fuera. Los métodos `getX` y `getY` siguen existiendo y devolviendo `double`, por lo que el código cliente no se rompe.

```java
public class Punto {
    // Implementación interna cambiada a un array
    // Oculto al exterior (private)
    private double[] coordenadas;

    public Punto(double x, double y) {
        // Inicializamos el array con tamaño 2
        this.coordenadas = new double[2];
        this.coordenadas[0] = x;
        this.coordenadas[1] = y;
    }

    // La interfaz pública NO cambia
    public double getX() {
        return this.coordenadas[0];
    }
    
    public double getY() {
        return this.coordenadas[1];
    }

    public double calcularDistanciaAOrigen() {
        // Usamos el array internamente
        return Math.sqrt(Math.pow(coordenadas[0], 2) + Math.pow(coordenadas[1], 2));
    }
}
```


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta

Aunque funcionalidad parezca la misma (acceder y modificar), **no** es lo mismo. Declararlo privado y acceder por métodos encapsula el atributo, lo que permite: 1) Cambiar la implementación interna en el futuro (si el atributo deja de existir pero se puede calcular); 2) Añadir lógica de validación en el setter; 3) Hacerlo de solo lectura eliminando el setter ni manteniendo el getter. Si fuera público, nada de esto sería posible sin romper el código cliente.

La convención habitual y recomendada ("Best Practice") es que **todos** los atributos sean privados (`private`). Solo se exponen a través de métodos si es estrictamente necesario. Esto está íntimamente relacionado con las **invariantes de clase**: si el atributo es público, la clase pierde el control sobre sus valores y no puede garantizar ninguna invariante. Con un setter, la clase puede rechazar valores que romperían su consistencia interna.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta

Una clase es **inmutable** si el estado de sus objetos no puede cambiar una vez que han sido construidos. No tiene métodos mutadores (setters u otros) y sus atributos suelen ser finales. Un **método modificador** (mutador) es cualquiera que altere el estado interno del objeto. No es siempre un "setter" (ej: `avanzar()`, `reiniciar()`), pero un "setter" sí es un tipo de mutador.

Las ventajas de la inmutabilidad son enormes: son automáticamente **thread-safe** (seguros en entornos concurrentes) porque no hay condiciones de carrera al no haber escrituras. Son más fáciles de entender y testear. Pueden ser usados de forma segura como claves en mapas (`HashMap`). Además, se pueden compartir referencias al mismo objeto sin miedo a que alguien lo modifique inesperadamente ("aliasing").


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta

No, absolutamente no. Poner getters y setters para *todos* los atributos privados de forma automática ("Java Bean style") rompe la encapsulación casi tanto como hacer los atributos públicos. Solo se deben crear setters para aquellos atributos que realmente necesiten ser modificados desde el exterior según el diseño de la clase.

Si un objeto debe ser inmutable o si un atributo es interno y no debe ser alterado arbitrariamente, no se debe incluir un setter. La interfaz pública debe ser diseñada pensando en *qué ofrece* el objeto, no en *qué contiene* el objeto. El uso indiscriminado de setters lleva a objetos anémicos que son meros contenedores de datos sin lógica ni protección de invariantes.


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta

La clase `String` en Java es **inmutable**. Una vez creado un objeto `String`, su contenido no puede cambiar. Cuando se concatenan dos cadenas (`str1 + str2`), no se modifica la primera, sino que se crea un **nuevo objeto** `String` con el resultado. La referencia original sigue apuntando a la cadena antigua si no se reasigna.

Si necesitamos concatenar muchas veces en un bucle, usar el operador `+` es muy ineficiente porque crea muchos objetos temporales intermedios que el recolector de basura debe limpiar, convirtiendo una operación lineal en una de complejidad cuadrática ($O(n^2)$). En su lugar, se debe utilizar la clase `StringBuilder` (o `StringBuffer` en contextos multihilo antiguos), que es mutable y permite ir añadiendo caracteres a un buffer interno sin crear nuevos objetos constantemente, siendo mucho más eficiente.


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

### Respuesta

Por defecto, el operador `==` en Java compara **identidad** de referencias, es decir, si dos variables apuntan exactamente al mismo objeto en memoria (la misma dirección), similar a comparar punteros en C. No compara el contenido.

El método `equals(Object o)` es el mecanismo estándar para comparar **contenido**. Por defecto (en la clase `Object`), `equals` se comporta igual que `==`, pero las clases deben sobrescribirlo para definir su propia igualdad lógica (ej: dos puntos son iguales si coinciden sus `x` e `y`). Para comparar cadenas, **siempre** se debe usar `cadena1.equals(cadena2)`, ya que `==` puede devolver `false` aunque contengan el mismo texto si son objetos distintos en memoria.


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

### Respuesta

Las clases "wrapper" (envoltorios) permiten tratar los tipos primitivos (como `int`, `double`, `boolean`) como si fueran objetos. En Java, por ejemplo, tenemos `Integer` para `int`, `Double` para `double`, etc. Esto es necesario porque muchas estructuras de datos y mecanismos de POO (como las colecciones `ArrayList` o los Generics) solo trabajan con referencias a objetos, no con primitivos.

Desde Java 5, este proceso es muy transparente gracias al "Autoboxing" y "Unboxing", que convierte automáticamente entre el primitivo y su wrapper según sea necesario (p.ej. asignar un `int` a un `Integer` lo envuelve automáticamente). La principal ventaja es la interoperabilidad con el sistema de objetos, aunque tiene un ligero coste de rendimiento. Otros lenguajes "puros" como Python o Ruby no tienen tipos primitivos diferenciados (todo es un objeto), mientras que C++ usa plantillas que admiten primitivos, por lo que no siempre necesitan wrappers explícitos como Java.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta

Un **tipo enumerado** (enum) es un tipo de dato especial que permite que una variable sea un conjunto de constantes predefinidas. En Java, un `enum` es mucho más que una lista de enteros con nombre (como en C); es una **clase** completa. Puede tener atributos, métodos y constructores.

Las ventajas en encapsulación son muy grandes: garantizan la **seguridad de tipos** (type safety), ya que es imposible asignar un valor fuera de los definidos (no puedes pasar un "13" a un método que espera un `Mes`). Además, al ser clases, pueden encapsular un comportamiento rico asociado a cada constante (como calcular si un mes es bisiesto o cuántos días tiene), en lugar de tener esa lógica dispersa por el código en sentencias `switch`.


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

### Respuesta

A continuación se presenta el enum `Mes`. Nótese cómo cada instancia (ENERO, etc.) se construye con su número de días. Usamos la lógica de que las estaciones incluyen los meses completos y el mes del cambio de estación (solsticios/equinoccios caen el 21, así que el mes "tiene días" de ambas estaciones).

```java
public enum Mes {
    ENERO(31), FEBRERO(28), MARZO(31), ABRIL(30), MAYO(31), JUNIO(30),
    JULIO(31), AGOSTO(31), SEPTIEMBRE(30), OCTUBRE(31), NOVIEMBRE(30), DICIEMBRE(31);

    // Atributo privado e inmutable
    private final int dias;

    // Constructor privado (solo invocado internamente al definir las constantes arriba)
    private Mes(int dias) {
        this.dias = dias;
    }

    public int getDias() {
        return dias;
    }

    // El ordinal empieza en 0, así que sumamos 1 para obtener 1-12
    public int getNumero() {
        return ordinal() + 1;
    }

    // Lógica de estaciones (aprox: Solsticios/Equinoccios son ~21 de Mar, Jun, Sep, Dic)
    // Invierno Norte: Dic, Ene, Feb, Mar
    // Primav. Norte: Mar, Abr, May, Jun
    // Verano Norte: Jun, Jul, Ago, Sep
    // Otoño Norte: Sep, Oct, Nov, Dic

    public boolean esDePrimavera(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == MARZO || this == ABRIL || this == MAYO || this == JUNIO;
        } else { // Primavera Sur = Otoño Norte
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE || this == DICIEMBRE;
        }
    }

    public boolean esDeVerano(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == JUNIO || this == JULIO || this == AGOSTO || this == SEPTIEMBRE;
        } else { // Verano Sur = Invierno Norte
            return this == DICIEMBRE || this == ENERO || this == FEBRERO || this == MARZO;
        }
    }

    public boolean esDeOtoño(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE || this == DICIEMBRE;
        } else { // Otoño Sur = Primavera Norte
            return this == MARZO || this == ABRIL || this == MAYO || this == JUNIO;
        }
    }

    public boolean esDeInvierno(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO || this == MARZO;
        } else { // Invierno Sur = Verano Norte
            return this == JUNIO || this == JULIO || this == AGOSTO || this == SEPTIEMBRE;
        }
    }
}
```
