# Laboratorio 2.3 (Adicional) — Una biblioteca para explorar las clases de Kotlin

<br/>
<br/>

## Descripción general

En este laboratorio construirás una pequeña **biblioteca digital de consola**. Cada parte del modelo sirve para experimentar con una forma de declarar clases en Kotlin y observar su comportamiento. No necesitas crear una aplicación Android: podrás concentrarte en el lenguaje y después reconocer estas mismas construcciones en proyectos Android.

> **Alcance:** “tipos de clases” es una expresión didáctica, no una lista cerrada de categorías excluyentes. `data`, `sealed`, `enum`, `value` y `annotation` son declaraciones especializadas; `open` y `abstract` describen posibilidades de herencia; `inner` y anidada describen ubicación y acceso. `object`, `companion object` e interfaces son construcciones relacionadas que también practicarás. Una clase puede combinar algunas características, pero no todas las combinaciones son válidas.

<br/>
<br/>

## Objetivos

Al finalizar podrás:

- Crear y distinguir clases ordinarias, `data`, `open`, `abstract`, `sealed`, `enum`, `value` y `annotation`.
- Diferenciar una clase anidada de una clase `inner`.
- Utilizar `object`, `companion object` y una expresión de objeto.
- Implementar interfaces y reconocer una `fun interface`.
- Elegir una construcción según el comportamiento que necesita el modelo.

<br/>
<br/>

## Prerrequisitos

### Conocimientos previos

- Variables `val` y `var`, funciones, constructores, `if`, `when` y colecciones.
- Lectura básica de código Kotlin. No se requieren bibliotecas externas.

<br/>
<br/>

### Acceso requerido

- Kotlin/JVM **1.9 o posterior** y un JDK instalados. Puedes usar el compilador `kotlinc` en Windows o un proyecto Kotlin/JVM en el IDE. La versión mínima propuesta permite experimentar con `data object` en la actividad opcional.
- Una terminal PowerShell o CMD. En un proyecto Gradle puedes ejecutar `main()` desde el IDE.

<br/>
<br/>

## Instrucciones

### Paso 1 — Crear el archivo y reconocer las categorías

Crea una carpeta `LaboratorioClasesKotlin` y, dentro, un archivo `Main.kt`. Al final del paso 2 pegarás un programa completo en ese archivo; los pasos siguientes te ayudarán a estudiarlo y modificarlo.

Antes de programar, predice qué usarías para cada necesidad:

| Necesidad de la biblioteca | Construcción que probarás |
|---|---|
| Libro con datos que se pueden copiar y comparar | `data class` |
| Identificador de libro que no debe confundirse con cualquier `Int` | `value class` |
| Estado entre opciones fijas | `enum class` |
| Resultado exitoso, vacío o con error y datos distintos por caso | `sealed class` |
| Material base que admite variantes | `open class` |
| Exportador que exige implementar una operación | `abstract class` |
| Ajustes compartidos | `object` |
| Fábrica asociada a `Libro` | `companion object` |
| Regla asociada a un libro concreto | `inner class` |
| Regla independiente de un libro concreto | clase anidada |

<br/>
<br/>

### Paso 2 — Construir el modelo completo

Copia **todo** el siguiente código en `Main.kt`. No pegues cada bloque en un archivo distinto: el programa completo evita referencias faltantes entre pasos.

```kotlin
@Target(AnnotationTarget.CLASS)
annotation class ModeloDidactico(val descripcion: String)

@JvmInline
value class LibroId(val valor: Int) {
    init { require(valor > 0) { "El ID debe ser positivo" } }
}

enum class Estado { DISPONIBLE, PRESTADO }

open class Material(val titulo: String) {
    open fun descripcion(): String = "Material: $titulo"
}

class LibroFisico(titulo: String, val paginas: Int) : Material(titulo) {
    override fun descripcion(): String = "$titulo ($paginas páginas)"
}

abstract class Exportador {
    abstract fun exportar(libro: Libro): String
}

class ExportadorTexto : Exportador() {
    override fun exportar(libro: Libro): String = "${libro.id.valor},${libro.titulo}"
}

interface Observador {
    fun avisar(mensaje: String)
}

fun interface FiltroLibro {
    fun aceptar(libro: Libro): Boolean
}

@ModeloDidactico("Entidad de ejemplo")
data class Libro(
    val id: LibroId,
    val titulo: String,
    val estado: Estado = Estado.DISPONIBLE
) {
    class ValidadorTitulo {
        fun esValido(titulo: String): Boolean = titulo.isNotBlank()
    }

    inner class Etiqueta {
        fun texto(): String = "${id.valor}: $titulo"
    }

    companion object {
        fun crear(id: Int, titulo: String): Libro = Libro(LibroId(id), titulo)
    }
}

sealed class ResultadoBusqueda {
    data class Encontrado(val libro: Libro) : ResultadoBusqueda()
    object Vacio : ResultadoBusqueda()
    data class Error(val motivo: String) : ResultadoBusqueda()
}

fun presentar(resultado: ResultadoBusqueda): String = when (resultado) {
    is ResultadoBusqueda.Encontrado -> "Encontrado: ${resultado.libro.titulo}"
    ResultadoBusqueda.Vacio -> "Sin resultados"
    is ResultadoBusqueda.Error -> "Error: ${resultado.motivo}"
}

object ConfiguracionBiblioteca {
    const val MAX_LIBROS = 100
}

class Catalogo {
    private val libros = mutableListOf<Libro>()

    fun agregar(libro: Libro) {
        require(libros.size < ConfiguracionBiblioteca.MAX_LIBROS)
        libros.add(libro)
    }

    fun buscar(id: LibroId): ResultadoBusqueda =
        libros.firstOrNull { it.id == id }
            ?.let { ResultadoBusqueda.Encontrado(it) }
            ?: ResultadoBusqueda.Vacio

    fun filtrar(filtro: FiltroLibro): List<Libro> = libros.filter { filtro.aceptar(it) }
}

fun ejecutarPruebas() {
    var aprobadas = 0
    fun comprobar(condicion: Boolean) {
        check(condicion) { "Falló la comprobación ${aprobadas + 1}" }
        aprobadas++
    }

    val libro = Libro.crear(1, "Kotlin básico")
    val copia = libro.copy(estado = Estado.PRESTADO)
    val catalogo = Catalogo()
    catalogo.agregar(libro)

    comprobar(libro == Libro(LibroId(1), "Kotlin básico"))        // data class
    comprobar(copia.estado == Estado.PRESTADO)                   // copy y enum
    comprobar(libro.estado == Estado.DISPONIBLE)                 // copia independiente
    comprobar(LibroFisico("Atlas", 80).descripcion().contains("80")) // open
    comprobar(ExportadorTexto().exportar(libro) == "1,Kotlin básico") // abstract
    comprobar(presentar(catalogo.buscar(LibroId(1))) == "Encontrado: Kotlin básico")
    comprobar(presentar(catalogo.buscar(LibroId(2))) == "Sin resultados")
    comprobar(presentar(ResultadoBusqueda.Error("red")) == "Error: red")
    comprobar(Libro.ValidadorTitulo().esValido("Kotlin"))       // anidada
    comprobar(libro.Etiqueta().texto() == "1: Kotlin básico")  // inner
    comprobar(ConfiguracionBiblioteca.MAX_LIBROS == 100)       // object
    comprobar(catalogo.filtrar(FiltroLibro { it.estado == Estado.DISPONIBLE }).size == 1)
    comprobar(LibroId(1) == libro.id)                           // value class

    println("Pruebas superadas: $aprobadas")
}

fun main(args: Array<String>) {
    if ("--test" in args) {
        ejecutarPruebas()
        return
    }

    val libro = Libro.crear(1, "Kotlin básico")
    val catalogo = Catalogo()
    catalogo.agregar(libro)

    val observador = object : Observador {
        override fun avisar(mensaje: String) = println("Aviso: $mensaje")
    }

    observador.avisar(presentar(catalogo.buscar(LibroId(1))))
    println(libro.Etiqueta().texto())
    println("Disponibles: ${catalogo.filtrar(FiltroLibro { it.estado == Estado.DISPONIBLE }).size}")
}
```

<br/>

**Nota:** `check()` pertenece a la biblioteca estándar de Kotlin; no requiere `import`. La función local `comprobar()` cuenta las verificaciones aprobadas y deja que `check()` lance una excepción cuando alguna falla.

<br/>
<br/>

### Paso 3 — Comparar la clase ordinaria con `data class`

Localiza `Catalogo` y `Libro`. `Catalogo` es una clase ordinaria que conserva una lista privada y ofrece operaciones. `Libro` es una clase de datos: Kotlin genera `equals()`, `hashCode()`, `toString()`, `componentN()` y `copy()` a partir de las propiedades del **constructor primario**.

1. Observa las tres primeras comprobaciones de `ejecutarPruebas()`.
2. Imprime `libro` y `copia`. Cambia el estado de la copia mediante `copy()` y comprueba que el original sigue disponible.
3. Opcionalmente crea dos instancias de `Catalogo` y compara `catalogo1 == catalogo2`. Una clase ordinaria no obtiene automáticamente la igualdad estructural de `data class`.

**Pregunta:** si `Libro` tuviera una lista mutable, ¿`copy()` duplicaría también los elementos de esa lista? Comprueba tu predicción: la copia generada es superficial.

<br/>
<br/>

### Paso 4 — Practicar `open`, `abstract` e interfaces

`Material` permite heredar porque lleva `open`; `descripcion()` también debe marcarse `open` para poder sobrescribirla. `LibroFisico` usa `override`. En cambio, `Exportador` es `abstract`: no se instancia directamente y obliga a implementar `exportar()` en una subclase concreta.

1. Cambia temporalmente el texto de `LibroFisico.descripcion()` y observa la cuarta comprobación.
2. Intenta quitar `open` de `Material` y lee el error de compilación; luego restaura el código.
3. Inspecciona `Observador` y `FiltroLibro`: son **interfaces**, no clases. Una `fun interface` tiene una sola función abstracta y permite crear una implementación con una lambda.


**Pregunta:** ¿cuándo usarías una clase abstracta con estado compartido y cuándo bastaría una interfaz que define un contrato?

<br/>
<br/>

### Paso 5 — Comparar `enum class` y `sealed class`

`Estado` define constantes del mismo tipo: `DISPONIBLE` y `PRESTADO`. `ResultadoBusqueda` agrupa variantes que pueden tener **datos diferentes**: `Encontrado` contiene un `Libro`, `Error` un motivo y `Vacio` no necesita datos.

1. Revisa las tres llamadas a `presentar()` en las comprobaciones.
2. Agrega temporalmente `object SinPermiso : ResultadoBusqueda()` y observa que `presentar()` exige cubrir este caso en `when`. Añade la rama y ejecútalo; después puedes restaurar el modelo original.
3. Sustituye opcionalmente `object Vacio` por `data object Vacio` (Kotlin 1.9+) y comprueba que el uso desde `when` sigue siendo el mismo.

**Importante:** las subclases directas de una clase `sealed` deben declararse en el mismo paquete y módulo; no es necesario que estén en el mismo archivo. Una clase `enum` ofrece constantes predefinidas; una jerarquía `sealed` admite variantes con estructuras distintas. Consulta las referencias oficiales al final del laboratorio.

<br/>
<br/>

### Paso 6 — Explorar `object`, `companion object` y el objeto anónimo

1. Encuentra el `object ConfiguracionBiblioteca`: se accede a `MAX_LIBROS` sin construir una instancia.
2. Encuentra `Libro.crear(...)`: pertenece al `companion object` de `Libro` y funciona como fábrica asociada a esa clase.
3. En `main()`, identifica `object : Observador { ... }`: es una **expresión de objeto** que construye una implementación anónima para ese uso concreto.

**Pregunta:** ¿cuáles de estos tres objetos tienen un nombre de declaración reutilizable y cuál se crea en el punto donde se necesita?

<br/>
<br/>

### Paso 7 — Comparar clase anidada e `inner`

`Libro.ValidadorTitulo()` no depende de una instancia de `Libro`. En cambio, `libro.Etiqueta()` necesita el libro exterior y puede leer `id` y `titulo`.

1. Localiza las dos comprobaciones correspondientes.
2. Intenta acceder a `titulo` desde `ValidadorTitulo` sin recibirlo como parámetro. Lee el error y restaura el método.
3. Explica por qué una instancia de `Etiqueta` conserva una referencia a su instancia exterior y considera esa referencia cuando se usa en código Android de larga duración.

<br/>
<br/>

### Paso 8 — Examinar `value class` y `annotation class`

`LibroId` da un tipo específico al identificador y valida que sea positivo. En Kotlin/JVM se declara aquí con `@JvmInline`; su representación en ejecución puede variar según el contexto, así que no presupongas que nunca se crea un objeto. `@ModeloDidactico` es una anotación propia aplicada a `Libro`: añade metadatos al código, pero **no ejecuta ninguna validación por sí sola**.

1. Intenta llamar `catalogo.buscar(1)` y observa el error de tipos; restaura `catalogo.buscar(LibroId(1))`.
2. Ejecuta `LibroId(0)` de forma temporal y observa el fallo de `require`.
3. Identifica el argumento `"Entidad de ejemplo"` que se pasa a la anotación. Para este laboratorio basta con reconocer su declaración y uso; no se necesita reflexión.

<br/>
<br/>

### Paso 9 — Ejecutar y probar el programa

Desde la carpeta que contiene `Main.kt`, en PowerShell o CMD:

```text
kotlinc Main.kt -include-runtime -d laboratorio-clases.jar
java -jar laboratorio-clases.jar
java -jar laboratorio-clases.jar --test
```

En la ejecución normal debes observar un aviso, una etiqueta y el número de libros disponibles. Con `--test` el resultado esperado es:

```text
Pruebas superadas: 13
```

<br/>

#### Prueba 1: Búsqueda exitosa

El libro con `LibroId(1)` produce `Encontrado: Kotlin básico`.


#### Prueba 2: Búsqueda vacía y error

`LibroId(2)` produce `Sin resultados`; una instancia de `ResultadoBusqueda.Error("red")` produce `Error: red`.


#### Prueba 3: Copia y selección

La copia cambia a `PRESTADO`, el libro original sigue `DISPONIBLE` y el filtro encuentra exactamente un libro.


<br/><br/>

### Paso 10 — Agregar pruebas unitarias sencillas

La función `ejecutarPruebas()` ya contiene 13 comprobaciones con `check`, sin instalar un framework. Agrega al menos **dos** comprobaciones propias: una para un título en blanco y otra para confirmar que el filtro no devuelve libros prestados. Actualiza el valor esperado de `Pruebas superadas` según las pruebas que añadas.

Como extensión opcional en un proyecto Gradle, transforma estas comprobaciones en pruebas con `kotlin.test` o JUnit. No necesitas hacerlo para completar este laboratorio.

<br/><br/>

### Paso 11 — Verificación final y limpieza

1. Asegúrate de que el programa compila y pasan todas las comprobaciones.
2. Elimina los cambios temporales que introdujiste para provocar errores.
3. En un `README.md` breve, anota **tres decisiones** del modelo: por qué `Libro` es `data`, por qué `Estado` es `enum` y por qué `ResultadoBusqueda` es `sealed`.
4. Guarda `Main.kt`, `README.md` y, si lo deseas, una captura de la salida. El archivo `.jar` se puede regenerar.

<br/><br/>

## Resumen de conceptos aplicados

| Construcción | En el laboratorio | Idea clave |
|---|---|---|
| Clase ordinaria | `Catalogo` | Estado y comportamiento propios |
| Clase `data` | `Libro` | Igualdad, representación y copia generadas |
| Clases `open` y `abstract` | `Material`, `Exportador` | Herencia permitida y contrato parcialmente implementado |
| Clase `enum` | `Estado` | Conjunto fijo de constantes |
| Clase `sealed` | `ResultadoBusqueda` | Variantes conocidas con datos distintos |
| Declaración y expresión `object` | `ConfiguracionBiblioteca`, observador | Instancia nombrada y objeto anónimo |
| `companion object` | `Libro.crear()` | Fábrica asociada a una clase |
| Clase anidada e `inner` | `ValidadorTitulo`, `Etiqueta` | Sin referencia o con referencia al exterior |
| `value class` | `LibroId` | Tipo específico para un valor |
| `annotation class` | `ModeloDidactico` | Metadatos declarativos |
| `interface` y `fun interface` | `Observador`, `FiltroLibro` | Contratos e implementación mediante lambda |

<br/><br/>

## Resumen

Las distintas declaraciones de Kotlin permiten expresar la intención del modelo: datos copiables, opciones fijas, resultados con variantes, herencia controlada y objetos asociados. Elegir entre ellas depende de la relación y el comportamiento que quieras representar.

<br/>

### Lo que lograste en este laboratorio

- Construiste un ejemplo ejecutable que combina las principales formas de clases de Kotlin y construcciones relacionadas.
- Comparaste sus comportamientos con comprobaciones concretas y errores de compilación deliberados.
- Practicaste cómo justificar una elección de diseño en lugar de memorizar palabras clave.

<br/>

### Recursos adicionales

- [Kotlin Classes: A Comprehensive Guide](https://dev.to/elozino/kotlin-classes-a-comprehensive-guide-33j6): artículo de referencia para comparar clases regulares, `data`, `sealed`, `enum`, objetos, compañeros e `inner`. **Nota de actualización:** su explicación de la ubicación de subclases `sealed` es demasiado restrictiva para Kotlin actual.

- [Classes — Kotlin Documentation](https://kotlinlang.org/docs/classes.html): declaración, constructores y propiedades de clases.

- [Data classes — Kotlin Documentation](https://kotlinlang.org/docs/data-classes.html): miembros generados, restricciones y comportamiento de `copy()`.

- [Sealed classes and interfaces — Kotlin Documentation](https://kotlinlang.org/docs/sealed-classes.html): jerarquías restringidas y expresiones `when` exhaustivas.

- [Enum classes — Kotlin Documentation](https://kotlinlang.org/docs/enum-classes.html): constantes, propiedades y métodos en enumeraciones.

- [Object declarations and expressions — Kotlin Documentation](https://kotlinlang.org/docs/object-declarations.html): objetos nombrados, compañeros y expresiones de objeto.

- [Nested and inner classes — Kotlin Documentation](https://kotlinlang.org/docs/nested-classes.html): acceso al objeto exterior y diferencias de instanciación.

- [Inline value classes — Kotlin Documentation](https://kotlinlang.org/docs/inline-classes.html): tipos de valor y particularidades de su representación en JVM.
