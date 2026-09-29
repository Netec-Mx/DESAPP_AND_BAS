# Laboratorio 2.2 (Adicional) — Kotlin esencial para comenzar con Android

<br/>

## Descripción general

En este laboratorio adicional crearás programas de consola en Kotlin: desde «Hola, mundo» hasta una lista de tareas. Al final reconocerás la sintaxis de lambdas que aparece en los listeners de Android. 

<br/>

## Objetivos

- Preparar Kotlin/JVM y ejecutar un programa.
- Utilizar variables, tipos, control de flujo y funciones.
- Tratar valores nulos de manera segura.
- Crear objetos y trabajar con colecciones.
- Escribir lambdas y comprender la sintaxis de la última lambda.

<br/>

## Prerrequisitos

### Conocimientos previos

Saber crear archivos, abrir una terminal y navegar a una carpeta. Se recomienda conocer los conceptos de variable y condición.

<br/>

### Acceso requerido

Un JDK, el compilador Kotlin/JVM y un editor. Este laboratorio usa PowerShell o CMD en Windows. Tener Android Studio instalado no garantiza que el comando `kotlinc` esté disponible en la terminal: para usar estos comandos descarga el compilador independiente. Consulta el procedimiento del paso 1.

<br/>

## Instrucciones

Crea la carpeta `LaboratorioKotlin` y un archivo `Main.kt`. En los pasos del 1 a 7 modifica el mismo archivo, sustituyendo el cuerpo de `main` para cada ejercicio. Conserva las funciones o clases que se indiquen. **Después de cada fase** repite los comandos de compilación y ejecución del paso 1. El paso 8 contiene una versión final completa para reemplazar los ejercicios temporales.

<br/>
<br/>

### Paso 1 —  Hola, mundo Kotlin

1. **Comprueba Java.** Abre una terminal nueva y ejecuta `java -version` y `javac -version`. Si alguno no existe, instala un JDK y configura su carpeta `bin` en `PATH`; un JRE por sí solo no incluye `javac`. Cierra y abre la terminal después de cambiar variables de entorno.

<br/>
<br/>

2. **Obtén `kotlinc`.** Ve a [Kotlin: compilador de línea de comandos](https://kotlinlang.org/docs/command-line.html), entra en el enlace a [JetBrains Kotlin Releases](https://github.com/JetBrains/kotlin/releases) y descarga el archivo **`kotlin-compiler-<versión>.zip`** de una versión estable. Elige el compilador JVM independiente; no descargues *Source code (zip)* ni Kotlin/Native. La versión del nombre cambiará con el tiempo.

<br/>
<br/>

3. **Descomprime.** Por ejemplo, extrae el ZIP de modo que exista `C:\Herramientas\Kotlin\kotlinc\bin\kotlinc.bat` (ajusta la ruta a tu equipo). Conserva toda la carpeta `kotlinc`, incluida `lib`.

<br/>
<br/>

4. **Agrega `bin` a tu `PATH` de usuario.** En Windows abre **Inicio → Editar las variables de entorno de esta cuenta → Variables de usuario → Path → Editar → Nuevo**. Pega la ruta real de `...\kotlinc\bin`, guarda las ventanas y abre una **terminal nueva**. Comprueba:

   ```cmd
   java -version
   javac -version
   kotlinc -version
   where kotlinc
   ```

<br/>
<br/>

5. **Prepara la carpeta.** En PowerShell o CMD ejecuta `mkdir LaboratorioKotlin` y luego `cd LaboratorioKotlin`. Crea ahí un archivo llamado **`Main.kt`**, no `Main.kt.txt`.  

<br/>
<br/>

6. **Escribe el programa** en `Main.kt`:

   ```kotlin
   fun main() {
       println("¡Hola, mundo Kotlin!")
   }
   ```

<br/>
<br/>

7. **Compila** desde la carpeta que contiene `Main.kt`:

   ```powershell
   kotlinc Main.kt -include-runtime -d laboratorio.jar
   ```

   `Main.kt` es el código fuente; `-d laboratorio.jar` da nombre al archivo generado; `-include-runtime` incorpora la biblioteca estándar de Kotlin para ejecutarlo con `java -jar`. Si no aparece ningún mensaje de error, verifica que se creó `laboratorio.jar` con `dir`.

   Verifica usando el comando `ls -lh` o simplemente `dir`.

<br/>
<br/>

8. **Ejecuta:**

   ```powershell
   java -jar laboratorio.jar
   ```

   **Esperado:** `¡Hola, mundo Kotlin!`. Cambia el saludo, guarda `Main.kt`, **compila de nuevo** y vuelve a ejecutar. El `.jar` anterior no se actualiza con solo guardar el código.

**Si algo falla:** «`kotlinc` no se reconoce» indica que debes revisar `PATH` y abrir otra terminal; «`Main.kt` no se encuentra» indica que la terminal está en otra carpeta o que el archivo tiene otra extensión; «`java` no se reconoce» requiere revisar la instalación del JDK y `PATH`; si el compilador muestra errores de sintaxis, corrige el código y repite la compilación antes de ejecutar.

<br/>
<br/>

### Paso 2 Variables y tipos

Reemplaza el cuerpo de `main`:

```kotlin

val nombre: String = "Gabriel"
var pendientes: Int = 2
val horas: Double = 1.5
val activo: Boolean = true

println("$nombre tiene $pendientes tareas; tiempo estimado: $horas horas")
println("${nombre} tiene ${pendientes} tareas; tiempo estimado: ${horas} horas")

pendientes += 1
println("¿Curso activo? $activo. Ahora hay $pendientes tareas")

pendientes ++
println("¿Curso activo? ${activo}. Ahora hay ${pendientes} tareas")

```

Compila y ejecuta. Cambia los valores. Intenta reasignar `nombre`, observa el error y deshaz ese intento. Explica por qué `pendientes` puede cambiar y `nombre` no.

<br/>
<br/>

### Paso 3 Control de flujo

Reemplaza el cuerpo de `main`:

```kotlin
val prioridad = 2

if (prioridad in 1..3) println("Prioridad válida")
else println("Prioridad fuera de rango")

val etiqueta = when (prioridad) {
    1 -> "Alta"
    2 -> "Media"
    3 -> "Baja"
    else -> "Desconocida"
}

println("Prioridad: $etiqueta")

for (numero in 1..3) println("Tarea $numero")
```

Ejecuta con `prioridad = 2` y luego con `4`. Señala qué ramas se ejecutan y qué imprime el ciclo.

<br/>
<br/>

### Paso 4 Funciones

Coloca estas funciones **fuera** de `main`:

```kotlin
fun esPrioridadValida(prioridad: Int): Boolean {
    return prioridad in 1..3
}

fun etiquetaPrioridad(prioridad: Int): String = when (prioridad) {
    1 -> "Alta"
    2 -> "Media"
    3 -> "Baja"
    else -> "Desconocida"
}
```

En `main`, prueba:

```kotlin
val prioridad = 1
println("¿Válida? ${esPrioridadValida(prioridad)}")
println("Etiqueta: ${etiquetaPrioridad(prioridad)}")
```

Repite con `3` y `5`. Identifica parámetros, tipos de retorno y la función de expresión única.

<br/>
<br/>

### Paso 5 Nulabilidad

Reemplaza el cuerpo de `main`:

Hasta ahora, las variables `String` siempre han contenido texto. En Kotlin, agrega `?` al tipo cuando una variable **puede contener `null`**. Reemplaza el cuerpo de `main` por este ejemplo:

```kotlin
val entrada: String? = null

println("Título recibido: $entrada")
println("Cantidad de caracteres: ${entrada?.length}")
println("Título para mostrar: ${entrada ?: "Sin título"}")
println("Longitud para mostrar: ${entrada?.length ?: 0}")

val otraEntrada: String? = "Estudiar Kotlin"

if (otraEntrada != null) {
    println("Otro título: $otraEntrada")
    println("Sus caracteres: ${otraEntrada.length}")
}
```

Compila y ejecuta. Observa que:

- `String?` permite guardar texto o `null`.
- `entrada?.length` consulta la longitud solo si `entrada` no es nula; en este caso produce `null`.
- `?:` proporciona un valor alternativo cuando la expresión de su izquierda produce `null`.
- Dentro de `if (otraEntrada != null)`, Kotlin sabe que puede consultar `otraEntrada.length`.

**Práctica:** cambia `entrada` por `"Mi primera tarea"` y ejecuta de nuevo. Después intenta escribir `entrada.length` sin comprobar si es nula: observa el error de compilación y restaura el código. Cambia también `entrada` por `""` para comprobar que **una cadena vacía no es lo mismo que `null`**: el operador `?:` no la sustituye por «Sin título».

<br/>
<br/>

### Paso 6 Clases y objetos

Conserva `etiquetaPrioridad` del paso 4. Añade esta clase **fuera** de `main`:

```kotlin
data class Tarea(
    val titulo: String,
    val prioridad: Int,
    var completada: Boolean = false
) {
    fun descripcion(): String =
        "$titulo | ${etiquetaPrioridad(prioridad)} | ${if (completada) "Hecha" else "Pendiente"}"
}
```

Prueba dentro de `main`:

```kotlin
val tarea = Tarea("Leer documentación", 2)
println(tarea.descripcion())
tarea.completada = true
println(tarea.descripcion())
```

Identifica constructor, propiedades, método y objeto. Comprueba que cambia de «Pendiente» a «Hecha».

<br/>
<br/>

### Paso 7 Colecciones

Conserva la clase y `etiquetaPrioridad`. Reemplaza el cuerpo de `main`:

```kotlin
val tareas = mutableListOf(
    Tarea("Leer documentación", 2),
    Tarea("Hacer ejercicio", 1, completada = true),
    Tarea("Revisar código", 3)
)
tareas.add(Tarea("Preparar práctica", 1))
tareas.forEach { tarea -> println(tarea.descripcion()) }
val pendientes = tareas.filter { tarea -> !tarea.completada }
val titulos = pendientes.map { tarea -> tarea.titulo }
println("Pendientes: $titulos")
```

Comprueba que «Hacer ejercicio» no figura entre los pendientes. Explica qué producen `filter` y `map`, y por qué la lista es mutable.

Una **lambda** es una función breve que se escribe sin darle un nombre. Sirve para indicar qué hacer con un dato que otra función le entrega.

Su forma básica es:

```kotlin
{ parametro -> acción }
```

En `tareas.forEach { tarea -> println(tarea.descripcion()) }`, la lambda recibe cada elemento de la lista como `tarea` e imprime su descripción.

<br/><br/>

### Paso 8 Lambdas e integración

Reemplaza **todo** `Main.kt` por esta versión final:

```kotlin
data class Tarea(
    val titulo: String,
    val prioridad: Int,
    var completada: Boolean = false
) {
    fun descripcion(): String =
        "$titulo | ${etiquetaPrioridad(prioridad)} | ${if (completada) "Hecha" else "Pendiente"}"
}

fun esPrioridadValida(prioridad: Int): Boolean = prioridad in 1..3

fun etiquetaPrioridad(prioridad: Int): String = when (prioridad) {
    1 -> "Alta"
    2 -> "Media"
    3 -> "Baja"
    else -> "Desconocida"
}

fun crearTarea(titulo: String?, prioridad: Int): Tarea? {
    val limpio = titulo?.trim()?.takeIf { it.isNotEmpty() } ?: return null
    if (!esPrioridadValida(prioridad)) return null
    return Tarea(limpio, prioridad)
}

fun pendientes(tareas: List<Tarea>): List<Tarea> =
    tareas.filter { tarea -> !tarea.completada }

fun ejecutarAccion(tarea: Tarea, accion: (Tarea) -> Unit) {
    accion(tarea)
}

fun ejecutarPruebas() {
    check(esPrioridadValida(1))
    check(!esPrioridadValida(4))
    check(etiquetaPrioridad(3) == "Baja")
    check(crearTarea(null, 1) == null)
    check(crearTarea("   ", 1) == null)
    check(crearTarea("Leer", 4) == null)
    check(crearTarea("  Leer  ", 1) == Tarea("Leer", 1))
    val tarea = Tarea("Estudiar", 2)
    ejecutarAccion(tarea) { item -> item.completada = true }
    check(tarea.completada)
    check(pendientes(listOf(tarea, Tarea("Practicar", 1))).size == 1)
    println("Pruebas superadas: 9")
}

fun main(args: Array<String>) {
    if (args.contains("--test")) {
        ejecutarPruebas()
        return
    }
    val tareas = mutableListOf<Tarea>()
    val entradas: List<Pair<String?, Int>> = listOf(
        "  Leer Kotlin  " to 1,
        "Practicar funciones" to 2,
        null to 3,
        "Revisar la app" to 3
    )
    for ((titulo, prioridad) in entradas) {
        crearTarea(titulo, prioridad)?.let { tareas.add(it) }
    }
    println("Todas las tareas:")
    tareas.forEach { tarea -> println(tarea.descripcion()) }

    // Lambda como último argumento, dentro de los paréntesis:
    ejecutarAccion(tareas[0], { tarea -> tarea.completada = true })
    // La misma sintaxis con la última lambda fuera de los paréntesis:
    ejecutarAccion(tareas[1]) { tarea -> tarea.completada = true }

    val titulosPendientes = pendientes(tareas).map { tarea -> tarea.titulo }
    println("Pendientes: $titulosPendientes")
}
```
<br/>

**Sintaxis de la última lambda:** `ejecutarAccion(tareas[1], { tarea -> ... })` y `ejecutarAccion(tareas[1]) { tarea -> ... }` entregan la misma lambda. Si el último parámetro de una función recibe otra función, la lambda puede escribirse después del paréntesis. Si es el único argumento, puedes omitir los paréntesis: `tareas.forEach { println(it.titulo) }`. En Android reconocerás una forma semejante en `boton.setOnClickListener { /* responder al clic */ }`.

<br/>
<br/>

### Paso 9 — Ejecutar y probar la aplicación

Compila con `kotlinc Main.kt -include-runtime -d laboratorio.jar` y ejecuta `java -jar laboratorio.jar`.

<br/>

#### Prueba 1: Creación y listado

Se muestran tres tareas válidas; los espacios exteriores del primer título desaparecen y la entrada `null` se omite.

<br/>

#### Prueba 2: Acciones y pendientes

La última línea debe ser `Pendientes: [Revisar la app]` porque las dos primeras tareas se marcaron como completadas.

<br/>

#### Prueba 3: Validación

Agrega temporalmente `"  " to 1` y `"Urgente" to 4` a `entradas`. Compila de nuevo y comprueba que ambas se omiten. 

<br/>
<br/>

### Paso 10 — Agregar pruebas unitarias

`ejecutarPruebas()` reúne nueve comprobaciones pequeñas mediante `check`, sin instalar un framework. Ejecuta `java -jar laboratorio.jar --test`. Debe aparecer `Pruebas superadas: 9`. Si una condición falla, el proceso termina con una excepción. Como extensión opcional en un proyecto Gradle, convierte los casos a `kotlin.test` o JUnit.

En este laboratorio, `check(condición)` continúa si la condición es verdadera; si es falsa, lanza una `IllegalStateException`. Por eso el mensaje Pruebas superadas: 9 solo aparece cuando pasan las nueve comprobaciones.

<br/>

### Paso 11 — Verificación final y limpieza

- Ejecuta el programa normal y las pruebas con `--test`.
- Confirma que `Main.kt` contiene una sola función `main` y que los fragmentos temporales fueron sustituidos por la versión final.
- Entrega `Main.kt` y una nota breve con comandos, salida y explicación de `?.`, `?:` y la última lambda.
- El `.jar` es regenerable; consérvalo solo si forma parte de la entrega.

<br/>
<br/>

## Resumen

Construiste un programa Kotlin capaz de validar entradas, representar tareas como objetos, marcarlas como completadas y listar las pendientes. Estos patrones aparecen después en el desarrollo Android.

<br/>

### Lo que lograste en este laboratorio

- Compilar y ejecutar Kotlin desde la terminal.
- Organizar la lógica en funciones y clases.
- Tratar valores ausentes sin acceso inseguro.
- Filtrar y transformar una colección.
- Reconocer y usar la sintaxis de la última lambda.
- Verificar resultados mediante comprobaciones automáticas sencillas.

<br/>

### Recursos adicionales

- [Compilador de Kotlin desde la línea de comandos](https://kotlinlang.org/docs/command-line.html) — instalación y ejecución de programas Kotlin/JVM.

- [Sintaxis básica de Kotlin](https://kotlinlang.org/docs/basic-syntax.html) — funciones, variables y estructuras básicas.

- [Seguridad ante valores nulos](https://kotlinlang.org/docs/null-safety.html) — tipos anulables, llamadas seguras y operador Elvis.

- [Colecciones de Kotlin](https://kotlinlang.org/docs/collections-overview.html) — listas y operaciones sobre colecciones.

- [Funciones de orden superior y lambdas](https://kotlinlang.org/docs/lambdas.html) — funciones como argumentos y sintaxis de la última lambda.

- [FAQ](https://developer.android.com/kotlin/faq?hl=es-419) — preguntas frecuentes sobre Kotlin en Android.

- [Guía de estilo Kotlin](https://developer.android.com/kotlin/style-guide?hl=es-419) — estándares de Google para dar formato al código y seguir convenciones de programación consistentes.

- [CodeLab](https://developer.android.com/codelabs/basic-android-kotlin-compose-first-program?hl=es-419#0) — codelab escrito por Google Developers Training team.

- [KotlinLang](https://play.kotlinlang.org/) - Kotlin online