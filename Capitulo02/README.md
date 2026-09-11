# Laboratorio 2 — Aplicación de fundamentos de Kotlin en un caso práctico

## 1. Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 216 minutos (≈ 3 h 36 min) |
| **Complejidad** | Media |
| **Nivel Bloom** | Aplicar (*Apply*) |

---

## 2. Descripción general

En este laboratorio construirás **KotlinTasks**, una aplicación Android de gestión de tareas pendientes (*To-Do List*) que aplica los fundamentos de Kotlin aprendidos en la lección 2.1: variables `val`/`var`, tipos de datos, inferencia de tipos, inmutabilidad y el sistema completo de null-safety (`?.`, `?:`, `!!`, `let`). Partirás de un proyecto nuevo basado en la plantilla *Empty Views Activity*, crearás un modelo de datos con `data class`, implementarás un repositorio en memoria con `companion object`, manipularás colecciones con operaciones funcionales, aplicarás expresiones de control para lógica de negocio y manejarás errores con `try-catch-finally`. La interfaz mostrará tareas en un `ScrollView` con `LinearLayout`, permitiendo agregar y filtrar tareas mediante `EditText` y `Button`.

---

## 3. Objetivos de aprendizaje

Al completar este laboratorio serás capaz de:

- [ ] Aplicar variables `val`/`var`, tipos de datos primitivos y de referencia, y el sistema de null-safety de Kotlin (`?.`, `!!`, `?:`, tipos nullable) en código Android real.
- [ ] Implementar funciones con parámetros tipados, valores por defecto, parámetros nombrados y funciones de extensión sobre clases de Android.
- [ ] Definir clases de datos (`data class`), clases selladas (`sealed class`), objetos `companion` y aplicar herencia e interfaces en un contexto Android.
- [ ] Manipular colecciones de Kotlin (`List`, `MutableList`, `Map`, `Set`) usando operaciones funcionales como `filter`, `map`, `forEach` y `sortedBy`.
- [ ] Utilizar expresiones de control `if`/`when`/`for`/`while` y manejo de errores `try-catch-finally` para implementar lógica de negocio en una aplicación Android funcional.

---

## 4. Prerrequisitos

### Conocimientos previos

| Requisito | Detalle |
|---|---|
| Laboratorio 1 completado | Android Studio Quail 3 instalado, AVDs configurados (API 30/35/36/37), proyecto *HolaAndroid* funcional |
| Configuración Gradle verificada | `libs.versions.toml` con Kotlin 2.2.10, AGP 9.3.2, Gradle 9.3.2 |
| Programación básica | Variables, funciones, condicionales y bucles en cualquier lenguaje |
| Estructura de proyecto Android | Comprensión de `AndroidManifest.xml`, `MainActivity.kt`, `activity_main.xml` |

### Acceso requerido

- Computadora con Android Studio Quail 3 (2026.1.3 Patch 1) instalado y funcional.
- Al menos un AVD operativo (se recomienda API 35 para las pruebas de este laboratorio).
- Conexión a Internet para resolución inicial de dependencias Gradle.

---

## 5. Entorno del laboratorio

### Hardware mínimo

| Componente | Especificación |
|---|---|
| Procesador | 64 bits, Intel i5 8.ª gen. / AMD Ryzen 5 o superior, VT-x/AMD-V habilitado |
| RAM | 16 GB mínimo (32 GB recomendado) |
| Disco | 50 GB libres en SSD |
| Pantalla | 1280×800 mínimo (1920×1080 recomendado) |
| GPU | Compatible con OpenGL ES 2.0+ |

### Software requerido

| Software | Versión |
|---|---|
| Android Studio | Quail 3 — 2026.1.3 Patch 1 |
| Kotlin | 2.2.10 |
| AGP | 9.3.2 |
| Gradle | 9.3.2 |
| JDK | 11 (`JavaVersion.VERSION_11`) |
| compileSdk / targetSdk | 37 |
| minSdk | 30 |
| Compose BOM | 2026.02.01 |
| Activity Compose | 1.13.0 |
| Core KTX | 1.19.0 |
| Lifecycle Runtime KTX | 2.6.1 |

### Directorio de trabajo

| Sistema operativo | Ruta |
|---|---|
| Windows | `C:\AndroidCursoBasico\Lab2\KotlinTasks` |
| macOS / Linux | `~/AndroidCursoBasico/Lab2/KotlinTasks` |

### Preparación inicial del directorio

**Windows (PowerShell):**

```powershell
New-Item -ItemType Directory -Force -Path "C:\AndroidCursoBasico\Lab2"
```

**macOS / Linux (Terminal):**

```bash
mkdir -p ~/AndroidCursoBasico/Lab2
```

---

## 6. Instrucciones paso a paso

### Paso 1 — Crear el proyecto KotlinTasks en Android Studio

**Objetivo:** Crear un nuevo proyecto Android con la plantilla *Empty Views Activity* y la configuración estándar del curso.

**Instrucciones:**

1. Abre **Android Studio Quail 3**.
2. Selecciona **File → New → New Project**.
3. En la lista de plantillas, elige **Empty Views Activity** (⚠️ **NO** selecciones *Empty Activity*, que genera un proyecto Compose).
4. Configura el proyecto con los siguientes valores:

   | Campo | Valor |
   |---|---|
   | **Name** | `KotlinTasks` |
   | **Package name** | `com.cursokotlin.android.kotlintasks` |
   | **Save location** | `C:\AndroidCursoBasico\Lab2\KotlinTasks` (Windows) o `~/AndroidCursoBasico/Lab2/KotlinTasks` (macOS/Linux) |
   | **Language** | Kotlin |
   | **Minimum SDK** | API 30: Android 11 (R) |
   | **Build configuration language** | Kotlin DSL (build.gradle.kts) |

5. Haz clic en **Finish** y espera a que Gradle sincronice completamente (observa la barra de progreso inferior).

**Resultado esperado:** El proyecto se abre con `MainActivity.kt` y `activity_main.xml` generados. La ventana de *Build* muestra `BUILD SUCCESSFUL`.

**Verificación:**

- En el panel *Project* (vista *Android*), confirma que existen:
  - `app/java/com.cursokotlin.android.kotlintasks/MainActivity`
  - `app/res/layout/activity_main.xml`
  - `Gradle Scripts/libs.versions.toml`

---

### Paso 2 — Configurar el catálogo de versiones y archivos Gradle

**Objetivo:** Establecer las versiones fijas del curso en `libs.versions.toml` y ajustar los archivos `build.gradle.kts` del proyecto y del módulo `app`.

**Instrucciones:**

1. Abre el archivo `gradle/libs.versions.toml` y **reemplaza todo su contenido** con lo siguiente:

```toml
[versions]
agp = "9.3.2"
kotlin = "2.2.10"
coreKtx = "1.19.0"
lifecycleRuntimeKtx = "2.6.1"
activityCompose = "1.13.0"
composeBom = "2026.02.01"
junit = "4.13.2"
junitExt = "1.3.0"
espressoCore = "3.7.0"
appcompat = "1.7.0"
material = "1.12.0"
constraintlayout = "2.2.1"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitExt" }
androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

2. Abre el archivo **`build.gradle.kts` de nivel proyecto** (raíz) y verifica que contenga:

```kotlin
// Top-level build file where you can add configuration options common to all sub-projects/modules.
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
}
```

3. Abre el archivo **`app/build.gradle.kts`** (módulo app) y **reemplaza todo su contenido** con:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "com.cursokotlin.android.kotlintasks"
    compileSdk = 37

    defaultConfig {
        applicationId = "com.cursokotlin.android.kotlintasks"
        minSdk = 30
        targetSdk = 37
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_11
        targetCompatibility = JavaVersion.VERSION_11
    }

    kotlinOptions {
        jvmTarget = "11"
    }
}

dependencies {
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.material)
    implementation(libs.androidx.constraintlayout)
    implementation(libs.androidx.lifecycle.runtime.ktx)

    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
}
```

4. Haz clic en **Sync Now** (o **File → Sync Project with Gradle Files**) y espera a que la sincronización finalice sin errores.

**Resultado esperado:** La barra inferior muestra `BUILD SUCCESSFUL` sin advertencias de versiones dinámicas.

**Verificación:**

- Abre **File → Project Structure → Modules → app** y confirma que `compileSdk = 37`, `minSdk = 30`, `targetSdk = 37`.
- En la pestaña *Build*, no deben aparecer errores ni warnings de compatibilidad.

---

### Paso 3 — Crear el modelo de datos con `data class` y `sealed class`

**Objetivo:** Definir la estructura de datos de la aplicación aplicando `data class`, `sealed class`, tipos nullable y variables `val`/`var`.

**Instrucciones:**

1. En el panel *Project* (vista *Android*), haz clic derecho sobre el paquete `com.cursokotlin.android.kotlintasks` → **New → Package**. Nombra el nuevo paquete: `model`.

2. Dentro del paquete `model`, crea un nuevo archivo Kotlin: clic derecho → **New → Kotlin Class/File** → selecciona **File** → nombre: `EstadoTarea`.

3. Escribe el siguiente contenido en `EstadoTarea.kt`:

```kotlin
package com.cursokotlin.android.kotlintasks.model

/**
 * Clase sellada que representa los posibles estados de una tarea.
 * Cada estado es un objeto singleton inmutable.
 * sealed class garantiza que no se puedan crear estados fuera de este archivo.
 */
sealed class EstadoTarea(val nombre: String) {
    object Pendiente : EstadoTarea("Pendiente")
    object EnProgreso : EstadoTarea("En Progreso")
    object Completada : EstadoTarea("Completada")
}
```

4. Dentro del paquete `model`, crea otro archivo Kotlin llamado `Tarea`. Escribe:

```kotlin
package com.cursokotlin.android.kotlintasks.model

/**
 * Data class que representa una tarea en la aplicación.
 *
 * - id: identificador único, inmutable (val).
 * - titulo: nombre de la tarea, inmutable (val).
 * - descripcion: detalle opcional, puede ser null (String?).
 * - estado: estado actual, mutable (var) porque cambia con el tiempo.
 * - prioridad: nivel de prioridad 1-5, inmutable con valor por defecto.
 * - fechaCreacion: timestamp en milisegundos, inmutable.
 */
data class Tarea(
    val id: Int,
    val titulo: String,
    val descripcion: String? = null,        // Tipo nullable con valor por defecto null
    var estado: EstadoTarea = EstadoTarea.Pendiente,  // Mutable: el estado cambia
    val prioridad: Int = 3,                 // Valor por defecto: prioridad media
    val fechaCreacion: Long = System.currentTimeMillis()
)
```

5. Dentro del paquete `model`, crea un archivo Kotlin llamado `TareaListener`:

```kotlin
package com.cursokotlin.android.kotlintasks.model

/**
 * Interfaz que define callbacks para eventos de la lista de tareas.
 */
interface TareaListener {
    fun onTareaAgregada(tarea: Tarea)
    fun onListaActualizada(total: Int)
    fun onError(mensaje: String)
}
```

**Resultado esperado:** Tres archivos creados en `model/` sin errores de compilación.

**Verificación:**

- Haz **Build → Make Project** (Ctrl+F9 / Cmd+F9). El resultado debe ser `BUILD SUCCESSFUL` con 0 errores.

---

### Paso 4 — Implementar el repositorio en memoria con `companion object`

**Objetivo:** Crear un repositorio que gestione la colección de tareas en memoria, aplicando `companion object`, `MutableList`, operaciones funcionales y manejo de errores.

**Instrucciones:**

1. Crea un nuevo paquete dentro de `com.cursokotlin.android.kotlintasks` llamado `repository`.

2. Dentro del paquete `repository`, crea un archivo Kotlin llamado `TareaRepository`:

```kotlin
package com.cursokotlin.android.kotlintasks.repository

import com.cursokotlin.android.kotlintasks.model.EstadoTarea
import com.cursokotlin.android.kotlintasks.model.Tarea

/**
 * Repositorio en memoria para gestionar tareas.
 * Usa companion object para mantener un estado compartido (patrón Singleton simplificado).
 */
class TareaRepository {

    companion object {
        // Lista mutable privada — solo el repositorio puede modificarla
        private val tareas: MutableList<Tarea> = mutableListOf()

        // Contador auto-incremental para IDs únicos
        private var siguienteId: Int = 1
    }

    /**
     * Agrega una nueva tarea a la lista.
     * Usa parámetros con valores por defecto y parámetros nombrados.
     *
     * @param titulo Nombre de la tarea (obligatorio).
     * @param descripcion Detalle opcional (nullable).
     * @param prioridad Nivel 1-5, por defecto 3.
     * @return La tarea creada.
     * @throws IllegalArgumentException si el título está vacío.
     */
    fun agregarTarea(
        titulo: String,
        descripcion: String? = null,
        prioridad: Int = 3
    ): Tarea {
        // Validación con require (lanza IllegalArgumentException)
        require(titulo.isNotBlank()) { "El título no puede estar vacío" }
        require(prioridad in 1..5) { "La prioridad debe estar entre 1 y 5" }

        val nuevaTarea = Tarea(
            id = siguienteId++,
            titulo = titulo.trim(),
            descripcion = descripcion?.trim(),  // Safe call: trim solo si no es null
            prioridad = prioridad
        )
        tareas.add(nuevaTarea)
        return nuevaTarea
    }

    /**
     * Obtiene todas las tareas como lista inmutable.
     * @return Lista de solo lectura de todas las tareas.
     */
    fun obtenerTodas(): List<Tarea> = tareas.toList()

    /**
     * Filtra tareas por estado usando operación funcional filter.
     * @param estado Estado por el cual filtrar.
     * @return Lista filtrada (inmutable).
     */
    fun filtrarPorEstado(estado: EstadoTarea): List<Tarea> {
        return tareas.filter { tarea -> tarea.estado == estado }
    }

    /**
     * Busca tareas cuyo título contenga el texto dado.
     * Usa operaciones funcionales filter y contains (ignoreCase).
     * @param texto Texto a buscar.
     * @return Lista de tareas que coinciden.
     */
    fun buscarPorTitulo(texto: String): List<Tarea> {
        return tareas.filter { it.titulo.contains(texto, ignoreCase = true) }
    }

    /**
     * Cambia el estado de una tarea por su ID.
     * Usa find (operación funcional) y el operador Elvis (?:).
     * @param id ID de la tarea.
     * @param nuevoEstado Nuevo estado a asignar.
     * @return true si se encontró y actualizó la tarea, false en caso contrario.
     */
    fun cambiarEstado(id: Int, nuevoEstado: EstadoTarea): Boolean {
        val tarea: Tarea? = tareas.find { it.id == id }
        return if (tarea != null) {
            tarea.estado = nuevoEstado
            true
        } else {
            false
        }
    }

    /**
     * Elimina una tarea por su ID.
     * @param id ID de la tarea a eliminar.
     * @return true si se eliminó, false si no se encontró.
     */
    fun eliminarTarea(id: Int): Boolean {
        return tareas.removeAll { it.id == id }
    }

    /**
     * Obtiene tareas ordenadas por prioridad (mayor prioridad primero: 5 → 1).
     * Usa sortedByDescending (operación funcional).
     * @return Lista ordenada (inmutable).
     */
    fun obtenerPorPrioridad(): List<Tarea> {
        return tareas.sortedByDescending { it.prioridad }
    }

    /**
     * Genera un resumen estadístico usando operaciones funcionales.
     * @return Mapa con conteo por estado.
     */
    fun obtenerResumen(): Map<String, Int> {
        return mapOf(
            "Pendientes" to tareas.count { it.estado is EstadoTarea.Pendiente },
            "En Progreso" to tareas.count { it.estado is EstadoTarea.EnProgreso },
            "Completadas" to tareas.count { it.estado is EstadoTarea.Completada },
            "Total" to tareas.size
        )
    }

    /**
     * Obtiene la descripción de una tarea de forma segura.
     * Demuestra el uso de safe call (?.), Elvis (?:) y let.
     * @param id ID de la tarea.
     * @return Descripción de la tarea o mensaje por defecto.
     */
    fun obtenerDescripcionSegura(id: Int): String {
        val tarea: Tarea? = tareas.find { it.id == id }
        // Encadenamiento de null-safety: ?. + let + ?:
        return tarea?.descripcion?.let { desc ->
            "Descripción: $desc"
        } ?: "Sin descripción disponible"
    }

    /**
     * Limpia todas las tareas (útil para testing).
     */
    fun limpiarTodas() {
        tareas.clear()
        siguienteId = 1
    }
}
```

**Resultado esperado:** El archivo `TareaRepository.kt` compila sin errores.

**Verificación:**

- Haz **Build → Make Project** (Ctrl+F9 / Cmd+F9). El resultado debe ser `BUILD SUCCESSFUL` con 0 errores.

---

### Paso 5 — Crear funciones de extensión y utilidades

**Objetivo:** Implementar funciones de extensión sobre tipos de Kotlin y Android, aplicando funciones con parámetros tipados y valores por defecto.

**Instrucciones:**

1. Crea un nuevo paquete dentro de `com.cursokotlin.android.kotlintasks` llamado `utils`.

2. Dentro del paquete `utils`, crea un archivo Kotlin llamado `Extensions`:

```kotlin
package com.cursokotlin.android.kotlintasks.utils

import android.widget.TextView
import com.cursokotlin.android.kotlintasks.model.EstadoTarea
import com.cursokotlin.android.kotlintasks.model.Tarea
import java.text.SimpleDateFormat
import java.util.Date
import java.util.Locale

/**
 * Función de extensión sobre String.
 * Capitaliza la primera letra y convierte el resto a minúsculas.
 */
fun String.capitalizar(): String {
    return if (this.isBlank()) {
        this
    } else {
        this[0].uppercaseChar() + this.substring(1).lowercase()
    }
}

/**
 * Función de extensión sobre Tarea.
 * Genera una representación formateada para mostrar en la UI.
 * Usa when (expresión de control) y null-safety.
 *
 * @param mostrarFecha Si se debe incluir la fecha de creación (valor por defecto: true).
 * @return String formateado con la información de la tarea.
 */
fun Tarea.formatearParaMostrar(mostrarFecha: Boolean = true): String {
    val simboloEstado: String = when (this.estado) {
        is EstadoTarea.Pendiente -> "⏳"
        is EstadoTarea.EnProgreso -> "🔄"
        is EstadoTarea.Completada -> "✅"
    }

    val simboloPrioridad: String = when {
        this.prioridad >= 4 -> "🔴"
        this.prioridad == 3 -> "🟡"
        else -> "🟢"
    }

    val descripcionTexto: String = this.descripcion?.let { desc ->
        "\n   📝 $desc"
    } ?: ""

    val fechaTexto: String = if (mostrarFecha) {
        val formato = SimpleDateFormat("dd/MM/yyyy HH:mm", Locale.getDefault())
        val fecha = formato.format(Date(this.fechaCreacion))
        "\n   📅 $fecha"
    } else {
        ""
    }

    return "$simboloEstado $simboloPrioridad [${this.id}] ${this.titulo}" +
            " (${this.estado.nombre})$descripcionTexto$fechaTexto"
}

/**
 * Función de extensión sobre List<Tarea>.
 * Genera un resumen de texto de la lista de tareas.
 *
 * @return String con todas las tareas formateadas, separadas por líneas.
 */
fun List<Tarea>.generarResumenTexto(): String {
    if (this.isEmpty()) return "No hay tareas para mostrar."

    val builder = StringBuilder()
    builder.appendLine("═══════════════════════════════════")
    builder.appendLine("       📋 LISTA DE TAREAS")
    builder.appendLine("═══════════════════════════════════")

    this.forEachIndexed { index, tarea ->
        builder.appendLine("${index + 1}. ${tarea.formatearParaMostrar()}")
        if (index < this.size - 1) {
            builder.appendLine("───────────────────────────────────")
        }
    }

    builder.appendLine("═══════════════════════════════════")
    builder.appendLine("Total: ${this.size} tarea(s)")

    return builder.toString()
}

/**
 * Función de extensión sobre TextView.
 * Muestra un mensaje de error con formato visual.
 *
 * @param mensaje Texto del error a mostrar.
 */
fun TextView.mostrarError(mensaje: String) {
    this.text = "⚠️ Error: $mensaje"
    this.setTextColor(android.graphics.Color.RED)
}

/**
 * Función de extensión sobre TextView.
 * Muestra un mensaje de éxito con formato visual.
 *
 * @param mensaje Texto del éxito a mostrar.
 */
fun TextView.mostrarExito(mensaje: String) {
    this.text = "✅ $mensaje"
    this.setTextColor(android.graphics.Color.parseColor("#2E7D32"))
}
```

**Resultado esperado:** El archivo `Extensions.kt` compila sin errores.

**Verificación:**

- Haz **Build → Make Project** (Ctrl+F9 / Cmd+F9). El resultado debe ser `BUILD SUCCESSFUL` con 0 errores.

---

### Paso 6 — Diseñar el layout XML de la interfaz

**Objetivo:** Crear la interfaz de usuario con `ScrollView`, `LinearLayout`, `EditText` y `Button` para interactuar con las tareas.

**Instrucciones:**

1. Abre el archivo `app/res/layout/activity_main.xml`.

2. Cambia al modo **Code** (pestaña inferior del editor de layout).

3. **Reemplaza todo el contenido** con el siguiente XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <!-- Título de la aplicación -->
        <TextView
            android:id="@+id/tvTitulo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="📋 KotlinTasks"
            android:textSize="24sp"
            android:textStyle="bold"
            android:textAlignment="center"
            android:layout_marginBottom="16dp" />

        <!-- Sección: Agregar tarea -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="12dp"
            app:cardElevation="4dp"
            app:cardCornerRadius="8dp"
            xmlns:app="http://schemas.android.com/apk/res-auto">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:padding="12dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="➕ Nueva Tarea"
                    android:textSize="16sp"
                    android:textStyle="bold"
                    android:layout_marginBottom="8dp" />

                <com.google.android.material.textfield.TextInputLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="8dp"
                    android:hint="Título de la tarea *"
                    xmlns:app="http://schemas.android.com/apk/res-auto"
                    app:boxBackgroundMode="outline">

                    <com.google.android.material.textfield.TextInputEditText
                        android:id="@+id/etTitulo"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:inputType="text"
                        android:maxLines="1" />

                </com.google.android.material.textfield.TextInputLayout>

                <com.google.android.material.textfield.TextInputLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="8dp"
                    android:hint="Descripción (opcional)"
                    xmlns:app="http://schemas.android.com/apk/res-auto"
                    app:boxBackgroundMode="outline">

                    <com.google.android.material.textfield.TextInputEditText
                        android:id="@+id/etDescripcion"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:inputType="textMultiLine"
                        android:maxLines="3" />

                </com.google.android.material.textfield.TextInputLayout>

                <com.google.android.material.textfield.TextInputLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="8dp"
                    android:hint="Prioridad (1-5, por defecto 3)"
                    xmlns:app="http://schemas.android.com/apk/res-auto"
                    app:boxBackgroundMode="outline">

                    <com.google.android.material.textfield.TextInputEditText
                        android:id="@+id/etPrioridad"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:inputType="number"
                        android:maxLines="1" />

                </com.google.android.material.textfield.TextInputLayout>

                <Button
                    android:id="@+id/btnAgregar"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="Agregar Tarea" />

            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Sección: Filtros -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="12dp"
            app:cardElevation="4dp"
            app:cardCornerRadius="8dp"
            xmlns:app="http://schemas.android.com/apk/res-auto">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:padding="12dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="🔍 Filtros"
                    android:textSize="16sp"
                    android:textStyle="bold"
                    android:layout_marginBottom="8dp" />

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal">

                    <Button
                        android:id="@+id/btnTodas"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Todas"
                        android:textSize="11sp"
                        android:layout_marginEnd="4dp"
                        style="?attr/materialButtonOutlinedStyle" />

                    <Button
                        android:id="@+id/btnPendientes"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Pend."
                        android:textSize="11sp"
                        android:layout_marginEnd="4dp"
                        style="?attr/materialButtonOutlinedStyle" />

                    <Button
                        android:id="@+id/btnEnProgreso"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Prog."
                        android:textSize="11sp"
                        android:layout_marginEnd="4dp"
                        style="?attr/materialButtonOutlinedStyle" />

                    <Button
                        android:id="@+id/btnCompletadas"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Compl."
                        android:textSize="11sp"
                        style="?attr/materialButtonOutlinedStyle" />

                </LinearLayout>

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:layout_marginTop="8dp">

                    <Button
                        android:id="@+id/btnPorPrioridad"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Por Prioridad"
                        android:textSize="11sp"
                        android:layout_marginEnd="4dp"
                        style="?attr/materialButtonOutlinedStyle" />

                    <Button
                        android:id="@+id/btnResumen"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Resumen"
                        android:textSize="11sp"
                        style="?attr/materialButtonOutlinedStyle" />

                </LinearLayout>

            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Sección: Búsqueda -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="12dp"
            app:cardElevation="4dp"
            app:cardCornerRadius="8dp"
            xmlns:app="http://schemas.android.com/apk/res-auto">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="horizontal"
                android:padding="12dp">

                <com.google.android.material.textfield.TextInputLayout
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:hint="Buscar por título..."
                    android:layout_marginEnd="8dp"
                    xmlns:app="http://schemas.android.com/apk/res-auto"
                    app:boxBackgroundMode="outline">

                    <com.google.android.material.textfield.TextInputEditText
                        android:id="@+id/etBuscar"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:inputType="text"
                        android:maxLines="1" />

                </com.google.android.material.textfield.TextInputLayout>

                <Button
                    android:id="@+id/btnBuscar"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="🔎"
                    android:layout_gravity="center_vertical" />

            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Sección: Cambiar estado -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="12dp"
            app:cardElevation="4dp"
            app:cardCornerRadius="8dp"
            xmlns:app="http://schemas.android.com/apk/res-auto">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:padding="12dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="🔄 Cambiar Estado"
                    android:textSize="16sp"
                    android:textStyle="bold"
                    android:layout_marginBottom="8dp" />

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:layout_marginBottom="8dp">

                    <com.google.android.material.textfield.TextInputLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:hint="ID de tarea"
                        android:layout_marginEnd="8dp"
                        xmlns:app="http://schemas.android.com/apk/res-auto"
                        app:boxBackgroundMode="outline">

                        <com.google.android.material.textfield.TextInputEditText
                            android:id="@+id/etIdTarea"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:inputType="number"
                            android:maxLines="1" />

                    </com.google.android.material.textfield.TextInputLayout>

                </LinearLayout>

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal">

                    <Button
                        android:id="@+id/btnMarcarProgreso"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="En Progreso"
                        android:textSize="11sp"
                        android:layout_marginEnd="4dp"
                        style="?attr/materialButtonOutlinedStyle" />

                    <Button
                        android:id="@+id/btnMarcarCompletada"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Completada"
                        android:textSize="11sp"
                        android:layout_marginEnd="4dp"
                        style="?attr/materialButtonOutlinedStyle" />

                    <Button
                        android:id="@+id/btnEliminar"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:text="Eliminar"
                        android:textSize="11sp"
                        style="?attr/materialButtonOutlinedStyle" />

                </LinearLayout>

            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Sección: Mensajes de estado -->
        <TextView
            android:id="@+id/tvMensaje"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text=""
            android:textSize="14sp"
            android:layout_marginBottom="8dp" />

        <!-- Sección: Lista de tareas -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            app:cardElevation="4dp"
            app:cardCornerRadius="8dp"
            xmlns:app="http://schemas.android.com/apk/res-auto">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:padding="12dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="📋 Tareas"
                    android:textSize="16sp"
                    android:textStyle="bold"
                    android:layout_marginBottom="8dp" />

                <TextView
                    android:id="@+id/tvListaTareas"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="No hay tareas aún. ¡Agrega tu primera tarea!"
                    android:textSize="13sp"
                    android:fontFamily="monospace"
                    android:lineSpacingExtra="4dp" />

            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Sección: Cargar datos de prueba -->
        <Button
            android:id="@+id/btnCargarDatos"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="📦 Cargar Datos de Prueba"
            android:layout_marginBottom="16dp"
            style="?attr/materialButtonOutlinedStyle" />

    </LinearLayout>
</ScrollView>
```

**Resultado esperado:** El layout se muestra en el panel *Design* sin errores de renderizado.

**Verificación:**

- Cambia a la pestaña **Design** y confirma que todos los elementos son visibles.
- Haz **Build → Make Project** (Ctrl+F9 / Cmd+F9). El resultado debe ser `BUILD SUCCESSFUL` con 0 errores.

---

### Paso 7 — Implementar la lógica de MainActivity

**Objetivo:** Conectar la interfaz con el repositorio, aplicando todas las características de Kotlin: null-safety, expresiones de control, manejo de errores, funciones de extensión y operaciones funcionales sobre colecciones.

**Instrucciones:**

1. Abre el archivo `app/java/com.cursokotlin.android.kotlintasks/MainActivity.kt`.

2. **Reemplaza todo su contenido** con el siguiente código:

```kotlin
package com.cursokotlin.android.kotlintasks

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.kotlintasks.model.EstadoTarea
import com.cursokotlin.android.kotlintasks.model.Tarea
import com.cursokotlin.android.kotlintasks.model.TareaListener
import com.cursokotlin.android.kotlintasks.repository.TareaRepository
import com.cursokotlin.android.kotlintasks.utils.formatearParaMostrar
import com.cursokotlin.android.kotlintasks.utils.generarResumenTexto
import com.cursokotlin.android.kotlintasks.utils.mostrarError
import com.cursokotlin.android.kotlintasks.utils.mostrarExito
import com.google.android.material.textfield.TextInputEditText

/**
 * Activity principal de KotlinTasks.
 * Implementa TareaListener para demostrar el uso de interfaces en Kotlin.
 */
class MainActivity : AppCompatActivity(), TareaListener {

    // Variables lateinit — se inicializan en onCreate (no pueden ser null después)
    private lateinit var etTitulo: TextInputEditText
    private lateinit var etDescripcion: TextInputEditText
    private lateinit var etPrioridad: TextInputEditText
    private lateinit var etBuscar: TextInputEditText
    private lateinit var etIdTarea: TextInputEditText
    private lateinit var tvMensaje: TextView
    private lateinit var tvListaTareas: TextView

    // Repositorio — val porque la referencia no cambia
    private val repository: TareaRepository = TareaRepository()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Inicializar vistas
        inicializarVistas()

        // Configurar listeners de botones
        configurarBotones()

        // Mostrar estado inicial
        actualizarListaTareas(repository.obtenerTodas())
    }

    /**
     * Inicializa todas las referencias a vistas del layout.
     * Usa findViewById — en un proyecto Views es el enfoque estándar.
     */
    private fun inicializarVistas() {
        etTitulo = findViewById(R.id.etTitulo)
        etDescripcion = findViewById(R.id.etDescripcion)
        etPrioridad = findViewById(R.id.etPrioridad)
        etBuscar = findViewById(R.id.etBuscar)
        etIdTarea = findViewById(R.id.etIdTarea)
        tvMensaje = findViewById(R.id.tvMensaje)
        tvListaTareas = findViewById(R.id.tvListaTareas)
    }

    /**
     * Configura los listeners de todos los botones.
     * Cada botón demuestra diferentes características de Kotlin.
     */
    private fun configurarBotones() {
        // Botón Agregar — demuestra try-catch, null-safety, parámetros nombrados
        findViewById<Button>(R.id.btnAgregar).setOnClickListener {
            agregarTarea()
        }

        // Botones de filtro — demuestran when y operaciones funcionales
        findViewById<Button>(R.id.btnTodas).setOnClickListener {
            actualizarListaTareas(repository.obtenerTodas())
            tvMensaje.mostrarExito("Mostrando todas las tareas")
        }

        findViewById<Button>(R.id.btnPendientes).setOnClickListener {
            val filtradas = repository.filtrarPorEstado(EstadoTarea.Pendiente)
            actualizarListaTareas(filtradas)
            tvMensaje.mostrarExito("Filtro: Pendientes (${filtradas.size})")
        }

        findViewById<Button>(R.id.btnEnProgreso).setOnClickListener {
            val filtradas = repository.filtrarPorEstado(EstadoTarea.EnProgreso)
            actualizarListaTareas(filtradas)
            tvMensaje.mostrarExito("Filtro: En Progreso (${filtradas.size})")
        }

        findViewById<Button>(R.id.btnCompletadas).setOnClickListener {
            val filtradas = repository.filtrarPorEstado(EstadoTarea.Completada)
            actualizarListaTareas(filtradas)
            tvMensaje.mostrarExito("Filtro: Completadas (${filtradas.size})")
        }

        // Botón Ordenar por prioridad
        findViewById<Button>(R.id.btnPorPrioridad).setOnClickListener {
            val ordenadas = repository.obtenerPorPrioridad()
            actualizarListaTareas(ordenadas)
            tvMensaje.mostrarExito("Ordenadas por prioridad (mayor a menor)")
        }

        // Botón Resumen — demuestra Map y operaciones funcionales
        findViewById<Button>(R.id.btnResumen).setOnClickListener {
            mostrarResumen()
        }

        // Botón Buscar — demuestra operaciones funcionales y null-safety
        findViewById<Button>(R.id.btnBuscar).setOnClickListener {
            buscarTareas()
        }

        // Botones de cambio de estado — demuestran when y manejo de errores
        findViewById<Button>(R.id.btnMarcarProgreso).setOnClickListener {
            cambiarEstadoTarea(EstadoTarea.EnProgreso)
        }

        findViewById<Button>(R.id.btnMarcarCompletada).setOnClickListener {
            cambiarEstadoTarea(EstadoTarea.Completada)
        }

        // Botón Eliminar
        findViewById<Button>(R.id.btnEliminar).setOnClickListener {
            eliminarTarea()
        }

        // Botón Cargar datos de prueba
        findViewById<Button>(R.id.btnCargarDatos).setOnClickListener {
            cargarDatosDePrueba()
        }
    }

    /**
     * Agrega una nueva tarea usando los datos del formulario.
     * Demuestra: try-catch-finally, null-safety (?., ?:), parámetros nombrados.
     */
    private fun agregarTarea() {
        try {
            // Obtener texto con safe call y Elvis operator
            val titulo: String = etTitulo.text?.toString()?.trim() ?: ""
            val descripcion: String? = etDescripcion.text?.toString()?.trim()
                ?.ifBlank { null }  // Convertir cadena vacía a null
            val prioridadTexto: String = etPrioridad.text?.toString()?.trim() ?: ""

            // Parsear prioridad con valor por defecto usando Elvis
            val prioridad: Int = prioridadTexto.toIntOrNull() ?: 3

            // Llamada con parámetros nombrados
            val nuevaTarea: Tarea = repository.agregarTarea(
                titulo = titulo,
                descripcion = descripcion,
                prioridad = prioridad
            )

            // Notificar mediante la interfaz
            onTareaAgregada(nuevaTarea)

            // Limpiar campos
            etTitulo.text?.clear()
            etDescripcion.text?.clear()
            etPrioridad.text?.clear()

        } catch (e: IllegalArgumentException) {
            // Manejo específico de errores de validación
            onError(e.message ?: "Error de validación desconocido")
        } catch (e: Exception) {
            // Manejo genérico de errores inesperados
            onError("Error inesperado: ${e.message}")
        } finally {
            // finally siempre se ejecuta — actualizar la lista
            actualizarListaTareas(repository.obtenerTodas())
        }
    }

    /**
     * Busca tareas por título.
     * Demuestra: null-safety, operaciones funcionales.
     */
    private fun buscarTareas() {
        val textoBusqueda: String = etBuscar.text?.toString()?.trim() ?: ""

        if (textoBusqueda.isBlank()) {
            tvMensaje.mostrarError("Ingresa un texto para buscar")
            return
        }

        val resultados: List<Tarea> = repository.buscarPorTitulo(textoBusqueda)
        actualizarListaTareas(resultados)

        // Uso de when como expresión
        val mensaje: String = when {
            resultados.isEmpty() -> "No se encontraron tareas con \"$textoBusqueda\""
            resultados.size == 1 -> "Se encontró 1 tarea con \"$textoBusqueda\""
            else -> "Se encontraron ${resultados.size} tareas con \"$textoBusqueda\""
        }
        tvMensaje.mostrarExito(mensaje)
    }

    /**
     * Cambia el estado de una tarea por su ID.
     * Demuestra: try-catch, when, null-safety, if como expresión.
     */
    private fun cambiarEstadoTarea(nuevoEstado: EstadoTarea) {
        try {
            val idTexto: String = etIdTarea.text?.toString()?.trim() ?: ""
            val id: Int = idTexto.toIntOrNull()
                ?: throw NumberFormatException("ID inválido")

            val exito: Boolean = repository.cambiarEstado(id, nuevoEstado)

            // if como expresión
            val mensaje: String = if (exito) {
                "Tarea #$id cambiada a ${nuevoEstado.nombre}"
            } else {
                "No se encontró la tarea con ID #$id"
            }

            if (exito) {
                tvMensaje.mostrarExito(mensaje)
            } else {
                tvMensaje.mostrarError(mensaje)
            }

            actualizarListaTareas(repository.obtenerTodas())

        } catch (e: NumberFormatException) {
            tvMensaje.mostrarError("Ingresa un ID numérico válido")
        }
    }

    /**
     * Elimina una tarea por su ID.
     * Demuestra: try-catch, null-safety.
     */
    private fun eliminarTarea() {
        try {
            val idTexto: String = etIdTarea.text?.toString()?.trim() ?: ""
            val id: Int = idTexto.toIntOrNull()
                ?: throw NumberFormatException("ID inválido")

            val eliminada: Boolean = repository.eliminarTarea(id)

            if (eliminada) {
                tvMensaje.mostrarExito("Tarea #$id eliminada correctamente")
            } else {
                tvMensaje.mostrarError("No se encontró la tarea con ID #$id")
            }

            actualizarListaTareas(repository.obtenerTodas())
            etIdTarea.text?.clear()

        } catch (e: NumberFormatException) {
            tvMensaje.mostrarError("Ingresa un ID numérico válido")
        }
    }

    /**
     * Muestra el resumen estadístico.
     * Demuestra: Map, forEach, StringBuilder.
     */
    private fun mostrarResumen() {
        val resumen: Map<String, Int> = repository.obtenerResumen()

        val builder = StringBuilder()
        builder.appendLine("═══════════════════════════════════")
        builder.appendLine("       📊 RESUMEN DE TAREAS")
        builder.appendLine("═══════════════════════════════════")

        // forEach sobre Map
        resumen.forEach { (clave, valor) ->
            val barra: String = "█".repeat(valor)
            builder.appendLine("$clave: $valor $barra")
        }

        builder.appendLine("═══════════════════════════════════")

        tvListaTareas.text = builder.toString()
        tvMensaje.mostrarExito("Resumen generado")
    }

    /**
     * Carga datos de ejemplo para facilitar las pruebas.
     * Demuestra: parámetros nombrados, valores por defecto, for loop.
     */
    private fun cargarDatosDePrueba() {
        // Limpiar datos existentes
        repository.limpiarTodas()

        // Crear tareas con diferentes combinaciones de parámetros
        // Tarea 1: todos los parámetros explícitos
        repository.agregarTarea(
            titulo = "Estudiar Kotlin básico",
            descripcion = "Repasar variables, tipos y null-safety",
            prioridad = 5
        )

        // Tarea 2: sin descripción (usa valor por defecto null)
        repository.agregarTarea(
            titulo = "Configurar Android Studio",
            prioridad = 4
        )

        // Tarea 3: solo título (usa todos los valores por defecto)
        repository.agregarTarea(titulo = "Hacer ejercicios del lab")

        // Tarea 4: con descripción, prioridad por defecto
        repository.agregarTarea(
            titulo = "Revisar sealed classes",
            descripcion = "Entender cuándo usar sealed vs enum"
        )

        // Tarea 5: prioridad baja
        repository.agregarTarea(
            titulo = "Leer documentación oficial",
            descripcion = "kotlinlang.org",
            prioridad = 1
        )

        // Cambiar estados de algunas tareas para demostrar variedad
        repository.cambiarEstado(2, EstadoTarea.EnProgreso)
        repository.cambiarEstado(5, EstadoTarea.Completada)

        // Actualizar UI
        actualizarListaTareas(repository.obtenerTodas())
        onListaActualizada(repository.obtenerTodas().size)
        tvMensaje.mostrarExito("Datos de prueba cargados (5 tareas)")
    }

    /**
     * Actualiza el TextView de la lista de tareas.
     * Usa la función de extensión generarResumenTexto().
     */
    private fun actualizarListaTareas(tareas: List<Tarea>) {
        tvListaTareas.text = tareas.generarResumenTexto()
        tvListaTareas.setTextColor(android.graphics.Color.DKGRAY)
    }

    // ─── Implementación de la interfaz TareaListener ───

    override fun onTareaAgregada(tarea: Tarea) {
        tvMensaje.mostrarExito("Tarea \"${tarea.titulo}\" agregada (ID: ${tarea.id})")
    }

    override fun onListaActualizada(total: Int) {
        // Uso de when como expresión para pluralización
        val textoTotal: String = when (total) {
            0 -> "No hay tareas"
            1 -> "1 tarea en total"
            else -> "$total tareas en total"
        }
        // Se podría usar para actualizar un contador en la UI
        println("DEBUG: $textoTotal")
    }

    override fun onError(mensaje: String) {
        tvMensaje.mostrarError(mensaje)
    }
}
```

**Resultado esperado:** `MainActivity.kt` compila sin errores y conecta toda la lógica de la aplicación.

**Verificación:**

- Haz **Build → Make Project** (Ctrl+F9 / Cmd+F9). El resultado debe ser `BUILD SUCCESSFUL` con 0 errores.

---

### Paso 8 — Ejecutar y probar la aplicación

**Objetivo:** Verificar que la aplicación funciona correctamente en el emulador, probando todas las funcionalidades implementadas.

**Instrucciones:**

1. Selecciona un AVD con **API 35** en la barra de herramientas superior.

2. Haz clic en **Run ▶** (Shift+F10 / Ctrl+R) para compilar e instalar la aplicación.

3. Una vez que la aplicación se abra en el emulador, realiza las siguientes pruebas en orden:

#### Prueba 1: Cargar datos de ejemplo

1. Desplázate hacia abajo y pulsa el botón **📦 Cargar Datos de Prueba**.
2. Verifica que aparecen 5 tareas en la sección "📋 Tareas".
3. Verifica que el mensaje muestra "✅ Datos de prueba cargados (5 tareas)".

#### Prueba 2: Agregar una tarea con todos los campos

1. En el campo **Título**, escribe: `Practicar funciones de extensión`.
2. En el campo **Descripción**, escribe: `Crear al menos 3 funciones de extensión propias`.
3. En el campo **Prioridad**, escribe: `4`.
4. Pulsa **Agregar Tarea**.
5. Verifica que la tarea aparece en la lista con ID 6, prioridad 🔴 y estado ⏳ Pendiente.

#### Prueba 3: Agregar una tarea solo con título (valores por defecto)

1. En el campo **Título**, escribe: `Tarea rápida`.
2. Deja **Descripción** y **Prioridad** vacíos.
3. Pulsa **Agregar Tarea**.
4. Verifica que la tarea aparece con prioridad 🟡 (3 por defecto) y sin descripción.

#### Prueba 4: Probar validación (título vacío)

1. Deja el campo **Título** vacío.
2. Pulsa **Agregar Tarea**.
3. Verifica que aparece el mensaje de error: "⚠️ Error: El título no puede estar vacío".

#### Prueba 5: Filtrar tareas

1. Pulsa **Pend.** → Verifica que solo aparecen tareas con estado ⏳ Pendiente.
2. Pulsa **Prog.** → Verifica que aparece la tarea "Configurar Android Studio" con estado 🔄.
3. Pulsa **Compl.** → Verifica que aparece "Leer documentación oficial" con estado ✅.
4. Pulsa **Todas** → Verifica que aparecen todas las tareas.

#### Prueba 6: Buscar tareas

1. En el campo de búsqueda, escribe: `Kotlin`.
2. Pulsa **🔎**.
3. Verifica que aparece "Estudiar Kotlin básico" en los resultados.

#### Prueba 7: Cambiar estado de una tarea

1. En el campo **ID de tarea**, escribe: `3`.
2. Pulsa **En Progreso**.
3. Verifica que la tarea #3 ahora muestra 🔄 En Progreso.
4. En el campo **ID de tarea**, escribe: `3`.
5. Pulsa **Completada**.
6. Verifica que la tarea #3 ahora muestra ✅ Completada.

#### Prueba 8: Eliminar una tarea

1. En el campo **ID de tarea**, escribe: `7`.
2. Pulsa **Eliminar**.
3. Verifica que la tarea #7 ya no aparece en la lista.

#### Prueba 9: Ver resumen

1. Pulsa **Resumen**.
2. Verifica que aparece un resumen con conteo por estado (Pendientes, En Progreso, Completadas, Total).

#### Prueba 10: Ordenar por prioridad

1. Pulsa **Por Prioridad**.
2. Verifica que las tareas se muestran ordenadas de mayor a menor prioridad (🔴 primero, 🟢 último).

**Resultado esperado:** Todas las 10 pruebas pasan correctamente. La aplicación responde sin crashes.

**Verificación:**

- La ventana **Logcat** (filtro: `com.cursokotlin.android.kotlintasks`) no muestra excepciones no controladas.
- Cada acción produce la respuesta visual esperada en la interfaz.

---

### Paso 9 — Agregar prueba unitaria para el repositorio

**Objetivo:** Crear una prueba unitaria básica que verifique la funcionalidad del repositorio, aplicando los conceptos de Kotlin en un contexto de testing.

**Instrucciones:**

1. En el panel *Project* (vista *Android*), navega a `app/java/com.cursokotlin.android.kotlintasks (test)` (la carpeta de tests unitarios, no la de `androidTest`).

2. Crea un nuevo archivo Kotlin llamado `TareaRepositoryTest`:

```kotlin
package com.cursokotlin.android.kotlintasks

import com.cursokotlin.android.kotlintasks.model.EstadoTarea
import com.cursokotlin.android.kotlintasks.model.Tarea
import com.cursokotlin.android.kotlintasks.repository.TareaRepository
import org.junit.Assert.assertEquals
import org.junit.Assert.assertFalse
import org.junit.Assert.assertNotNull
import org.junit.Assert.assertNull
import org.junit.Assert.assertTrue
import org.junit.Before
import org.junit.Test

/**
 * Pruebas unitarias para TareaRepository.
 * Verifican el correcto funcionamiento de las operaciones CRUD
 * y las operaciones funcionales sobre colecciones.
 */
class TareaRepositoryTest {

    // lateinit — se inicializa en @Before
    private lateinit var repository: TareaRepository

    @Before
    fun setUp() {
        repository = TareaRepository()
        repository.limpiarTodas()
    }

    @Test
    fun agregarTarea_conTituloValido_retornaTareaConIdAsignado() {
        // Arrange & Act
        val tarea: Tarea = repository.agregarTarea(titulo = "Test tarea")

        // Assert
        assertNotNull(tarea)
        assertEquals("Test tarea", tarea.titulo)
        assertNull(tarea.descripcion)  // Valor por defecto: null
        assertEquals(3, tarea.prioridad)  // Valor por defecto: 3
        assertTrue(tarea.estado is EstadoTarea.Pendiente)  // Estado inicial
    }

    @Test
    fun agregarTarea_conTodosLosParametros_retornaTareaCompleta() {
        // Act — usando parámetros nombrados
        val tarea: Tarea = repository.agregarTarea(
            titulo = "Tarea completa",
            descripcion = "Con descripción",
            prioridad = 5
        )

        // Assert
        assertEquals("Tarea completa", tarea.titulo)
        assertEquals("Con descripción", tarea.descripcion)
        assertEquals(5, tarea.prioridad)
    }

    @Test(expected = IllegalArgumentException::class)
    fun agregarTarea_conTituloVacio_lanzaExcepcion() {
        // Act — debe lanzar IllegalArgumentException
        repository.agregarTarea(titulo = "")
    }

    @Test(expected = IllegalArgumentException::class)
    fun agregarTarea_conPrioridadInvalida_lanzaExcepcion() {
        // Act — prioridad fuera de rango
        repository.agregarTarea(titulo = "Test", prioridad = 6)
    }

    @Test
    fun filtrarPorEstado_retornaSoloTareasConEseEstado() {
        // Arrange
        repository.agregarTarea(titulo = "Tarea 1")
        repository.agregarTarea(titulo = "Tarea 2")
        repository.agregarTarea(titulo = "Tarea 3")
        repository.cambiarEstado(2, EstadoTarea.EnProgreso)

        // Act
        val pendientes: List<Tarea> = repository.filtrarPorEstado(EstadoTarea.Pendiente)
        val enProgreso: List<Tarea> = repository.filtrarPorEstado(EstadoTarea.EnProgreso)

        // Assert
        assertEquals(2, pendientes.size)
        assertEquals(1, enProgreso.size)
        assertEquals("Tarea 2", enProgreso[0].titulo)
    }

    @Test
    fun buscarPorTitulo_encuentraTareasParciales() {
        // Arrange
        repository.agregarTarea(titulo = "Estudiar Kotlin")
        repository.agregarTarea(titulo = "Practicar Java")
        repository.agregarTarea(titulo = "Repasar Kotlin avanzado")

        // Act
        val resultados: List<Tarea> = repository.buscarPorTitulo("Kotlin")

        // Assert
        assertEquals(2, resultados.size)
    }

    @Test
    fun cambiarEstado_conIdValido_retornaTrue() {
        // Arrange
        repository.agregarTarea(titulo = "Test")

        // Act
        val resultado: Boolean = repository.cambiarEstado(1, EstadoTarea.Completada)

        // Assert
        assertTrue(resultado)
        val tarea: Tarea = repository.obtenerTodas()[0]
        assertTrue(tarea.estado is EstadoTarea.Completada)
    }

    @Test
    fun cambiarEstado_conIdInvalido_retornaFalse() {
        // Act
        val resultado: Boolean = repository.cambiarEstado(999, EstadoTarea.Completada)

        // Assert
        assertFalse(resultado)
    }

    @Test
    fun eliminarTarea_conIdValido_retornaTrue() {
        // Arrange
        repository.agregarTarea(titulo = "Para eliminar")

        // Act
        val eliminada: Boolean = repository.eliminarTarea(1)

        // Assert
        assertTrue(eliminada)
        assertEquals(0, repository.obtenerTodas().size)
    }

    @Test
    fun obtenerPorPrioridad_retornaOrdenDescendente() {
        // Arrange
        repository.agregarTarea(titulo = "Baja", prioridad = 1)
        repository.agregarTarea(titulo = "Alta", prioridad = 5)
        repository.agregarTarea(titulo = "Media", prioridad = 3)

        // Act
        val ordenadas: List<Tarea> = repository.obtenerPorPrioridad()

        // Assert
        assertEquals("Alta", ordenadas[0].titulo)
        assertEquals("Media", ordenadas[1].titulo)
        assertEquals("Baja", ordenadas[2].titulo)
    }

    @Test
    fun obtenerResumen_retornaConteosCorrecto() {
        // Arrange
        repository.agregarTarea(titulo = "T1")
        repository.agregarTarea(titulo = "T2")
        repository.agregarTarea(titulo = "T3")
        repository.cambiarEstado(2, EstadoTarea.EnProgreso)
        repository.cambiarEstado(3, EstadoTarea.Completada)

        // Act
        val resumen: Map<String, Int> = repository.obtenerResumen()

        // Assert
        assertEquals(1, resumen["Pendientes"])
        assertEquals(1, resumen["En Progreso"])
        assertEquals(1, resumen["Completadas"])
        assertEquals(3, resumen["Total"])
    }

    @Test
    fun obtenerDescripcionSegura_conDescripcion_retornaFormateada() {
        // Arrange
        repository.agregarTarea(titulo = "Test", descripcion = "Mi descripción")

        // Act
        val resultado: String = repository.obtenerDescripcionSegura(1)

        // Assert
        assertEquals("Descripción: Mi descripción", resultado)
    }

    @Test
    fun obtenerDescripcionSegura_sinDescripcion_retornaMensajePorDefecto() {
        // Arrange
        repository.agregarTarea(titulo = "Test")

        // Act
        val resultado: String = repository.obtenerDescripcionSegura(1)

        // Assert
        assertEquals("Sin descripción disponible", resultado)
    }
}
```

3. Para ejecutar las pruebas, haz clic derecho sobre el archivo `TareaRepositoryTest` → **Run 'TareaRepositoryTest'**.

**Resultado esperado:** Todas las 12 pruebas pasan (barra verde).

**Verificación:**

- La ventana *Run* muestra: `Tests passed: 12 of 12`.
- No hay pruebas fallidas ni errores de compilación.

---

### Paso 10 — Verificación final y limpieza

**Objetivo:** Confirmar que todo el proyecto compila, las pruebas pasan y la aplicación funciona correctamente.

**Instrucciones:**

1. Ejecuta **Build → Clean Project**.
2. Ejecuta **Build → Rebuild Project**.
3. Verifica que el resultado es `BUILD SUCCESSFUL`.
4. Ejecuta las pruebas unitarias nuevamente: clic derecho sobre `TareaRepositoryTest` → **Run 'TareaRepositoryTest'**. Confirma que las 12 pruebas pasan.
5. Ejecuta la aplicación en el emulador (Run ▶) y repite al menos las pruebas 1, 2, 4 y 9 del Paso 8.

**Resultado esperado:** Compilación exitosa, 12 pruebas unitarias pasando, aplicación funcional sin crashes.

**Verificación final:**

| Verificación | Estado esperado |
|---|---|
| Build → Rebuild Project | `BUILD SUCCESSFUL` |
| Pruebas unitarias | 12 de 12 pasando (barra verde) |
| App se inicia sin crash | Pantalla principal visible |
| Cargar datos de prueba | 5 tareas mostradas |
| Agregar tarea | Tarea aparece en la lista |
| Validación título vacío | Mensaje de error mostrado |
| Filtros funcionan | Tareas filtradas correctamente |
| Búsqueda funciona | Resultados correctos |
| Cambio de estado | Estado actualizado en la lista |
| Resumen | Conteos correctos |

---

## 7. Resumen de conceptos aplicados

| Concepto de Kotlin | Dónde se aplicó |
|---|---|
| `val` / `var` | `Tarea.kt` (propiedades inmutables/mutables), `MainActivity.kt` (variables) |
| Tipos de datos | `Int`, `String`, `Long`, `Boolean` en todo el proyecto |
| Inferencia de tipos | Variables locales en `MainActivity.kt` y `TareaRepository.kt` |
| Tipos nullable (`String?`) | `Tarea.descripcion`, parámetros de `agregarTarea()` |
| Safe call (`?.`) | `etTitulo.text?.toString()`, `descripcion?.trim()` |
| Elvis operator (`?:`) | Valores por defecto en `agregarTarea()`, `obtenerDescripcionSegura()` |
| `let` | `obtenerDescripcionSegura()`, `formatearParaMostrar()` |
| `data class` | `Tarea.kt` |
| `sealed class` | `EstadoTarea.kt` |
| `companion object` | `TareaRepository.kt` (estado compartido) |
| Interface | `TareaListener.kt`, implementada en `MainActivity` |
| Funciones de extensión | `Extensions.kt` (`formatearParaMostrar`, `mostrarError`, etc.) |
| Parámetros por defecto | `agregarTarea()`, `formatearParaMostrar()` |
| Parámetros nombrados | Llamadas a `agregarTarea()` en `cargarDatosDePrueba()` |
| `when` (expresión) | `formatearParaMostrar()`, `buscarTareas()`, `onListaActualizada()` |
| `if` como expresión | `cambiarEstadoTarea()`, `capitalizar()` |
| `for` / `forEach` | `mostrarResumen()`, `generarResumenTexto()` |
| `filter` / `map` / `sortedBy` | `TareaRepository.kt` (filtrar, buscar, ordenar) |
| `MutableList` / `List` | `TareaRepository.kt` (colección mutable interna, retorno inmutable) |
| `Map` | `obtenerResumen()` |
| `try-catch-finally` | `agregarTarea()` en `MainActivity.kt` |
| `require` | Validación en `TareaRepository.agregarTarea()` |
| `lateinit` | Referencias a vistas en `MainActivity.kt` |

---

## 8. Solución de problemas comunes

| Problema | Causa probable | Solución |
|---|---|---|
| Error `Unresolved reference: R` | Gradle no ha generado la clase R | Ejecuta **Build → Clean Project** y luego **Build → Rebuild Project** |
| Error en `TextInput
