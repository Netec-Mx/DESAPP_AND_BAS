# Laboratorio 3 — Navegación y Ciclo de Vida entre Pantallas Android

## Metadatos del Laboratorio

| Campo | Valor |
|---|---|
| **Duración** | 216 minutos (aprox. 3 h 36 min) |
| **Complejidad** | Media |
| **Nivel Bloom** | Aplicar |
| **Proyecto** | TaskManager |
| **Paquete base** | `com.cursokotlin.android.taskmanager` |

---

## Descripción General

En este laboratorio transformarás el proyecto conceptual del Laboratorio 2 en una aplicación Android multi-pantalla llamada **TaskManager**. Implementarás cuatro Activities interconectadas mediante Intents explícitos, instrumentarás el ciclo de vida completo con logs en Logcat, manejarás la persistencia de estado ante rotaciones con `onSaveInstanceState`, establecerás comunicación bidireccional con `ActivityResultLauncher` y separarás la lógica de datos con un `ViewModel` básico. Finalmente, configurarás el `AndroidManifest.xml` declarando correctamente cada componente.

---

## Objetivos de Aprendizaje

Al completar este laboratorio serás capaz de:

- [ ] Verificar experimentalmente los callbacks del ciclo de vida de una Activity (`onCreate` → `onDestroy`) mediante logs en Logcat.
- [ ] Implementar navegación entre múltiples Activities usando Intents explícitos con paso de datos mediante extras.
- [ ] Preservar el estado de un formulario ante rotaciones de pantalla usando `onSaveInstanceState` / `onRestoreInstanceState`.
- [ ] Establecer comunicación bidireccional entre Activities con `ActivityResultLauncher` y `registerForActivityResult`.
- [ ] Introducir un `ViewModel` con `LiveData` para que la lista de tareas sobreviva cambios de configuración.

---

## Prerrequisitos

### Conocimientos Previos

| Requisito | Fuente |
|---|---|
| Estructura de un proyecto Android (manifiesto, recursos, Gradle) | Lección 3.1 |
| Data classes, sealed classes, null-safety y colecciones en Kotlin | Laboratorio 2 |
| Creación de un proyecto con plantilla *Empty Views Activity* | Laboratorio 1 |
| Uso básico de Logcat y AVDs | Laboratorio 1 |

### Acceso Requerido

- Android Studio Quail 3 (2026.1.3 Patch 1) instalado y funcional.
- AVDs configurados para API 30 y API 37 (mínimo uno operativo).
- Conexión a Internet para descarga de dependencias Gradle.

---

## Entorno del Laboratorio

### Hardware Mínimo

| Componente | Requisito |
|---|---|
| Procesador | 64-bit con VT-x / AMD-V habilitado |
| RAM | 16 GB mínimo |
| Disco | 50 GB libres en SSD |
| Pantalla | 1280×800 mínimo |

### Software Requerido

| Herramienta | Versión |
|---|---|
| Android Studio | Quail 3 — 2026.1.3 Patch 1 |
| Kotlin | 2.2.10 |
| AGP | 9.3.2 |
| Gradle | 9.3.2 |
| JDK | 11 (`JavaVersion.VERSION_11`) |
| compileSdk / targetSdk | 37 |
| minSdk | 30 |
| Core KTX | 1.19.0 |
| Lifecycle Runtime KTX | 2.6.1 |
| Activity Compose | 1.13.0 (disponible, no usada directamente) |
| Compose BOM | 2026.02.01 (disponible, no usada directamente) |
| AppCompat | 1.7.0 |
| Material | 1.12.0 |
| ConstraintLayout | 2.2.1 |
| Lifecycle ViewModel KTX | 2.6.1 |
| LiveData KTX | 2.6.1 |

### Directorio de Trabajo

| SO | Ruta |
|---|---|
| Windows | `C:\AndroidCursoBasico\Lab3\TaskManager` |
| macOS / Linux | `~/AndroidCursoBasico/Lab3/TaskManager` |

---

## Paso 1 — Crear el Proyecto TaskManager

### Objetivo

Crear un nuevo proyecto Android con la plantilla **Empty Views Activity** y configurar completamente los archivos Gradle y el catálogo de versiones.

### Instrucciones

1. Abre **Android Studio Quail 3**.

2. Selecciona **File → New → New Project**.

3. En la lista de plantillas, elige **Empty Views Activity** (no *Empty Activity*).

4. Configura el proyecto con los siguientes datos:

   | Campo | Valor |
   |---|---|
   | Name | `TaskManager` |
   | Package name | `com.cursokotlin.android.taskmanager` |
   | Save location | `C:\AndroidCursoBasico\Lab3\TaskManager` (Windows) o `~/AndroidCursoBasico/Lab3/TaskManager` (macOS/Linux) |
   | Language | Kotlin |
   | Minimum SDK | API 30: Android 11 (R) |
   | Build configuration language | Kotlin DSL |

5. Haz clic en **Finish** y espera a que finalice la sincronización Gradle.

6. Abre el archivo `gradle/libs.versions.toml` y reemplaza su contenido completo con:

```toml
[versions]
agp = "9.3.2"
kotlin = "2.2.10"
coreKtx = "1.19.0"
lifecycleRuntimeKtx = "2.6.1"
lifecycleViewmodelKtx = "2.6.1"
lifecycleLivedataKtx = "2.6.1"
activityCompose = "1.13.0"
composeBom = "2026.02.01"
appcompat = "1.7.0"
material = "1.12.0"
constraintlayout = "2.2.1"
junit = "4.13.2"
junitExt = "1.3.0"
espressoCore = "3.7.0"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-lifecycle-viewmodel-ktx = { group = "androidx.lifecycle", name = "lifecycle-viewmodel-ktx", version.ref = "lifecycleViewmodelKtx" }
androidx-lifecycle-livedata-ktx = { group = "androidx.lifecycle", name = "lifecycle-livedata-ktx", version.ref = "lifecycleLivedataKtx" }
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

7. Abre el archivo `build.gradle.kts` **de nivel raíz** y verifica que contenga:

```kotlin
// build.gradle.kts (raíz del proyecto)
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
}
```

8. Abre el archivo `app/build.gradle.kts` y reemplaza su contenido con:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "com.cursokotlin.android.taskmanager"
    compileSdk = 37

    defaultConfig {
        applicationId = "com.cursokotlin.android.taskmanager"
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

    buildFeatures {
        viewBinding = true
    }
}

dependencies {
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.material)
    implementation(libs.androidx.constraintlayout)
    implementation(libs.androidx.lifecycle.runtime.ktx)
    implementation(libs.androidx.lifecycle.viewmodel.ktx)
    implementation(libs.androidx.lifecycle.livedata.ktx)

    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
}
```

9. Haz clic en **Sync Now** en la barra amarilla que aparece en la parte superior del editor.

10. Abre `settings.gradle.kts` y verifica que el nombre del proyecto sea correcto:

```kotlin
pluginManagement {
    repositories {
        google {
            content {
                includeGroupByRegex("com\\.android.*")
                includeGroupByRegex("com\\.google.*")
                includeGroupByRegex("androidx.*")
            }
        }
        mavenCentral()
        gradlew()
    }
}
dependencyResolution {
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "TaskManager"
include(":app")
```

> **Nota:** El contenido exacto de `settings.gradle.kts` puede variar ligeramente según la versión de Android Studio. Lo importante es que `rootProject.name = "TaskManager"` y que los repositorios `google()` y `mavenCentral()` estén presentes.

### Resultado Esperado

La sincronización Gradle finaliza sin errores. El proyecto aparece en el explorador con la estructura estándar: `app/src/main/kotlin/`, `app/src/main/res/`, y `AndroidManifest.xml`.

### Verificación

- En la pestaña **Build** de la parte inferior de Android Studio, confirma que no hay errores en rojo.
- Ejecuta el proyecto en un AVD API 37 (o API 30). Debe mostrar la pantalla predeterminada con "Hello World!".

---

## Paso 2 — Crear el Modelo de Datos Tarea

### Objetivo

Definir la data class `Tarea` y la sealed class `EstadoTarea` que servirán como modelo de datos para toda la aplicación.

### Instrucciones

1. En el explorador de Android Studio, haz clic derecho sobre el paquete `com.cursokotlin.android.taskmanager` → **New → Package**. Nómbralo `model`.

2. Dentro del paquete `model`, crea un nuevo archivo Kotlin: **New → Kotlin Class/File → File**, nómbralo `Tarea`.

3. Escribe el siguiente contenido en `Tarea.kt`:

```kotlin
package com.cursokotlin.android.taskmanager.model

/**
 * Representa los posibles estados de una tarea.
 */
sealed class EstadoTarea(val nombre: String) {
    data object Pendiente : EstadoTarea("Pendiente")
    data object EnProgreso : EstadoTarea("En Progreso")
    data object Completada : EstadoTarea("Completada")

    companion object {
        fun fromString(valor: String): EstadoTarea = when (valor) {
            "Pendiente" -> Pendiente
            "En Progreso" -> EnProgreso
            "Completada" -> Completada
            else -> Pendiente
        }
    }
}

/**
 * Modelo principal de datos para una tarea.
 */
data class Tarea(
    val id: Int,
    val titulo: String,
    val descripcion: String,
    val estado: EstadoTarea = EstadoTarea.Pendiente
)
```

4. Crea otro paquete llamado `repository` dentro del paquete base.

5. Dentro de `repository`, crea el archivo `TareaRepository.kt`:

```kotlin
package com.cursokotlin.android.taskmanager.repository

import com.cursokotlin.android.taskmanager.model.EstadoTarea
import com.cursokotlin.android.taskmanager.model.Tarea

/**
 * Repositorio en memoria que gestiona la lista de tareas.
 */
object TareaRepository {

    private val tareas = mutableListOf(
        Tarea(1, "Configurar entorno", "Instalar Android Studio y crear AVDs", EstadoTarea.Completada),
        Tarea(2, "Aprender Kotlin", "Estudiar data classes y colecciones", EstadoTarea.EnProgreso),
        Tarea(3, "Crear TaskManager", "Implementar navegación entre pantallas", EstadoTarea.Pendiente)
    )

    private var nextId = 4

    fun obtenerTodas(): List<Tarea> = tareas.toList()

    fun obtenerPorId(id: Int): Tarea? = tareas.find { it.id == id }

    fun agregar(titulo: String, descripcion: String): Tarea {
        val nueva = Tarea(
            id = nextId++,
            titulo = titulo,
            descripcion = descripcion,
            estado = EstadoTarea.Pendiente
        )
        tareas.add(nueva)
        return nueva
    }

    fun cantidad(): Int = tareas.size
}
```

### Resultado Esperado

El proyecto compila sin errores. Los paquetes `model` y `repository` aparecen correctamente en el explorador.

### Verificación

Ejecuta **Build → Make Project** (`Ctrl+F9` / `Cmd+F9`). El resultado debe ser `BUILD SUCCESSFUL`.

---

## Paso 3 — Instrumentar el Ciclo de Vida en MainActivity

### Objetivo

Agregar logs de depuración en cada callback del ciclo de vida de `MainActivity` para observar su comportamiento en Logcat.

### Instrucciones

1. Abre el archivo `MainActivity.kt` ubicado en `com.cursokotlin.android.taskmanager`.

2. Reemplaza su contenido completo con el siguiente código:

```kotlin
package com.cursokotlin.android.taskmanager

import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanager.databinding.ActivityMainBinding
import com.cursokotlin.android.taskmanager.repository.TareaRepository

class MainActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "MainActivity_Lifecycle"
    }

    private lateinit var binding: ActivityMainBinding

    // ── Ciclo de vida ──────────────────────────────────────

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Mostrar cantidad de tareas como verificación inicial
        val cantidad = TareaRepository.cantidad()
        binding.textViewInfo.text = "TaskManager — $cantidad tareas cargadas"

        Log.d(TAG, "onCreate: $cantidad tareas en repositorio")
    }

    override fun onStart() {
        super.onStart()
        Log.d(TAG, ">>> onStart() llamado")
    }

    override fun onResume() {
        super.onResume()
        Log.d(TAG, ">>> onResume() llamado — Activity VISIBLE e INTERACTIVA")
    }

    override fun onPause() {
        super.onPause()
        Log.d(TAG, ">>> onPause() llamado — Activity pierde el foco")
    }

    override fun onStop() {
        super.onStop()
        Log.d(TAG, ">>> onStop() llamado — Activity NO visible")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d(TAG, ">>> onRestart() llamado — Activity regresa de onStop")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, ">>> onDestroy() llamado — Activity DESTRUIDA")
    }
}
```

3. Abre el archivo `res/layout/activity_main.xml` y reemplaza su contenido con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layoutMain"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center_horizontal"
    android:padding="24dp">

    <TextView
        android:id="@+id/textViewInfo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="TaskManager"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginTop="32dp" />

    <TextView
        android:id="@+id/textViewListaTareas"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:layout_marginTop="16dp"
        android:textSize="14sp"
        android:lineSpacingExtra="4dp"
        android:text="Cargando tareas..." />

    <Button
        android:id="@+id/buttonCrearTarea"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Crear Nueva Tarea"
        android:layout_marginTop="8dp" />

    <Button
        android:id="@+id/buttonAcercaDe"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Acerca De"
        android:layout_marginTop="8dp"
        android:layout_marginBottom="16dp" />

</LinearLayout>
```

4. Ejecuta la aplicación en el AVD API 37.

5. Abre la ventana **Logcat** en Android Studio (pestaña inferior).

6. En el campo de filtro de Logcat, escribe: `tag:MainActivity_Lifecycle`

7. Observa los logs que aparecen al iniciar la app. Deberías ver:

### Resultado Esperado

```
D  >>> onCreate() llamado
D  onCreate: 3 tareas en repositorio
D  >>> onStart() llamado
D  >>> onResume() llamado — Activity VISIBLE e INTERACTIVA
```

8. Ahora realiza las siguientes acciones y observa los logs en cada caso:

   | Acción | Logs esperados |
   |---|---|
   | Presionar botón **Home** del emulador | `onPause` → `onStop` |
   | Volver a abrir la app desde recientes | `onRestart` → `onStart` → `onResume` |
   | **Rotar** el dispositivo (`Ctrl+←` / `Ctrl+→`) | `onPause` → `onStop` → `onDestroy` → `onCreate` → `onStart` → `onResume` |
   | Presionar botón **Back** | `onPause` → `onStop` → `onDestroy` |

### Verificación

- Confirma que cada acción produce exactamente la secuencia de logs descrita en la tabla.
- La rotación del dispositivo genera un ciclo completo de destrucción y recreación: esto demuestra por qué se pierde el estado sin mecanismos de preservación.

---

## Paso 4 — Crear Activities Adicionales y Navegación con Intents

### Objetivo

Crear tres Activities adicionales (`DetallesTareaActivity`, `CrearTareaActivity`, `AcercaDeActivity`) y navegar entre ellas usando Intents explícitos con paso de datos.

### Instrucciones

#### 4.1 — Crear AcercaDeActivity

1. Haz clic derecho sobre el paquete `com.cursokotlin.android.taskmanager` → **New → Activity → Empty Views Activity**.

2. Configura:
   - Activity Name: `AcercaDeActivity`
   - Layout Name: `activity_acerca_de`
   - Marca la casilla **Launcher Activity**: **NO**

3. Reemplaza el contenido de `res/layout/activity_acerca_de.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="32dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="TaskManager"
        android:textSize="28sp"
        android:textStyle="bold" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Versión 1.0"
        android:textSize="18sp"
        android:layout_marginTop="8dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Laboratorio 3 — Curso Android Básico"
        android:textSize="14sp"
        android:layout_marginTop="16dp"
        android:gravity="center" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Navegación y Ciclo de Vida de Activities"
        android:textSize="14sp"
        android:layout_marginTop="4dp"
        android:gravity="center" />

    <Button
        android:id="@+id/buttonVolver"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Volver"
        android:layout_marginTop="32dp" />

</LinearLayout>
```

4. Reemplaza el contenido de `AcercaDeActivity.kt`:

```kotlin
package com.cursokotlin.android.taskmanager

import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanager.databinding.ActivityAcercaDeBinding

class AcercaDeActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "AcercaDe_Lifecycle"
    }

    private lateinit var binding: ActivityAcercaDeBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityAcercaDeBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.buttonVolver.setOnClickListener {
            finish() // Cierra esta Activity y regresa a la anterior en el back stack
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, ">>> onDestroy() llamado")
    }
}
```

#### 4.2 — Crear DetallesTareaActivity

5. Crea una nueva **Empty Views Activity** llamada `DetallesTareaActivity` con layout `activity_detalles_tarea`.

6. Reemplaza `res/layout/activity_detalles_tarea.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Detalles de la Tarea"
        android:textSize="22sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/textViewId"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:textSize="14sp" />

    <TextView
        android:id="@+id/textViewTitulo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:textSize="18sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/textViewDescripcion"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:textSize="16sp" />

    <TextView
        android:id="@+id/textViewEstado"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:textSize="16sp" />

    <Button
        android:id="@+id/buttonVolverDetalles"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Volver"
        android:layout_marginTop="32dp" />

</LinearLayout>
```

7. Reemplaza `DetallesTareaActivity.kt`:

```kotlin
package com.cursokotlin.android.taskmanager

import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanager.databinding.ActivityDetallesTareaBinding

class DetallesTareaActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "Detalles_Lifecycle"
        // Claves constantes para los extras del Intent
        const val EXTRA_TAREA_ID = "extra_tarea_id"
        const val EXTRA_TAREA_TITULO = "extra_tarea_titulo"
        const val EXTRA_TAREA_DESCRIPCION = "extra_tarea_descripcion"
        const val EXTRA_TAREA_ESTADO = "extra_tarea_estado"
    }

    private lateinit var binding: ActivityDetallesTareaBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityDetallesTareaBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Recuperar datos enviados desde MainActivity
        val tareaId = intent.getIntExtra(EXTRA_TAREA_ID, -1)
        val titulo = intent.getStringExtra(EXTRA_TAREA_TITULO) ?: "Sin título"
        val descripcion = intent.getStringExtra(EXTRA_TAREA_DESCRIPCION) ?: "Sin descripción"
        val estado = intent.getStringExtra(EXTRA_TAREA_ESTADO) ?: "Desconocido"

        Log.d(TAG, "Recibido: id=$tareaId, titulo=$titulo, estado=$estado")

        // Mostrar datos en la interfaz
        binding.textViewId.text = "ID: $tareaId"
        binding.textViewTitulo.text = titulo
        binding.textViewDescripcion.text = descripcion
        binding.textViewEstado.text = "Estado: $estado"

        binding.buttonVolverDetalles.setOnClickListener {
            finish()
        }
    }
}
```

#### 4.3 — Crear CrearTareaActivity

8. Crea una nueva **Empty Views Activity** llamada `CrearTareaActivity` con layout `activity_crear_tarea`.

9. Reemplaza `res/layout/activity_crear_tarea.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Crear Nueva Tarea"
        android:textSize="22sp"
        android:textStyle="bold" />

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:hint="Título de la tarea">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editTextTitulo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="text"
            android:maxLines="1" />

    </com.google.android.material.textfield.TextInputLayout>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:hint="Descripción">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editTextDescripcion"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="textMultiLine"
            android:minLines="3" />

    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="24dp"
        android:gravity="end">

        <Button
            android:id="@+id/buttonCancelar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Cancelar"
            style="?attr/materialButtonOutlinedStyle"
            android:layout_marginEnd="12dp" />

        <Button
            android:id="@+id/buttonGuardar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Guardar" />

    </LinearLayout>

</LinearLayout>
```

10. Reemplaza `CrearTareaActivity.kt`:

```kotlin
package com.cursokotlin.android.taskmanager

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanager.databinding.ActivityCrearTareaBinding

class CrearTareaActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "CrearTarea_Lifecycle"
        const val RESULT_TAREA_TITULO = "result_tarea_titulo"
        const val RESULT_TAREA_DESCRIPCION = "result_tarea_descripcion"
    }

    private lateinit var binding: ActivityCrearTareaBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityCrearTareaBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.buttonGuardar.setOnClickListener {
            val titulo = binding.editTextTitulo.text.toString().trim()
            val descripcion = binding.editTextDescripcion.text.toString().trim()

            if (titulo.isEmpty()) {
                binding.editTextTitulo.error = "El título es obligatorio"
                return@setOnClickListener
            }

            Log.d(TAG, "Guardando tarea: titulo=$titulo")

            // Devolver resultado a la Activity que invocó
            val resultIntent = Intent().apply {
                putExtra(RESULT_TAREA_TITULO, titulo)
                putExtra(RESULT_TAREA_DESCRIPCION, descripcion)
            }
            setResult(Activity.RESULT_OK, resultIntent)
            finish()
        }

        binding.buttonCancelar.setOnClickListener {
            setResult(Activity.RESULT_CANCELED)
            finish()
        }
    }
}
```

#### 4.4 — Conectar la Navegación en MainActivity

11. Regresa a `MainActivity.kt` y actualiza el código completo para incluir la navegación:

```kotlin
package com.cursokotlin.android.taskmanager

import android.content.Intent
import android.os.Bundle
import android.util.Log
import android.widget.Toast
import androidx.activity.result.ActivityResultLauncher
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanager.databinding.ActivityMainBinding
import com.cursokotlin.android.taskmanager.model.Tarea
import com.cursokotlin.android.taskmanager.repository.TareaRepository

class MainActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "MainActivity_Lifecycle"
    }

    private lateinit var binding: ActivityMainBinding

    // Launcher para recibir resultado de CrearTareaActivity
    private lateinit var crearTareaLauncher: ActivityResultLauncher<Intent>

    // ── Ciclo de vida ──────────────────────────────────────

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Registrar el launcher ANTES de que la Activity esté en STARTED
        registrarLauncher()

        // Configurar botones de navegación
        configurarNavegacion()

        // Mostrar lista de tareas
        actualizarListaTareas()
    }

    override fun onStart() {
        super.onStart()
        Log.d(TAG, ">>> onStart() llamado")
    }

    override fun onResume() {
        super.onResume()
        Log.d(TAG, ">>> onResume() llamado — Activity VISIBLE e INTERACTIVA")
    }

    override fun onPause() {
        super.onPause()
        Log.d(TAG, ">>> onPause() llamado — Activity pierde el foco")
    }

    override fun onStop() {
        super.onStop()
        Log.d(TAG, ">>> onStop() llamado — Activity NO visible")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d(TAG, ">>> onRestart() llamado — Activity regresa de onStop")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, ">>> onDestroy() llamado — Activity DESTRUIDA")
    }

    // ── Métodos privados ───────────────────────────────────

    private fun registrarLauncher() {
        crearTareaLauncher = registerForActivityResult(
            ActivityResultContracts.StartActivityForResult()
        ) { resultado ->
            if (resultado.resultCode == RESULT_OK) {
                val data = resultado.data
                val titulo = data?.getStringExtra(CrearTareaActivity.RESULT_TAREA_TITULO) ?: ""
                val descripcion = data?.getStringExtra(CrearTareaActivity.RESULT_TAREA_DESCRIPCION) ?: ""

                if (titulo.isNotEmpty()) {
                    val nuevaTarea = TareaRepository.agregar(titulo, descripcion)
                    Log.d(TAG, "Nueva tarea creada: ${nuevaTarea.id} - ${nuevaTarea.titulo}")
                    Toast.makeText(this, "Tarea '${nuevaTarea.titulo}' creada", Toast.LENGTH_SHORT).show()
                    actualizarListaTareas()
                }
            } else {
                Log.d(TAG, "Creación de tarea cancelada")
            }
        }
    }

    private fun configurarNavegacion() {
        // Botón: Crear nueva tarea (con resultado)
        binding.buttonCrearTarea.setOnClickListener {
            val intent = Intent(this, CrearTareaActivity::class.java)
            crearTareaLauncher.launch(intent)
        }

        // Botón: Acerca de (navegación simple)
        binding.buttonAcercaDe.setOnClickListener {
            val intent = Intent(this, AcercaDeActivity::class.java)
            startActivity(intent)
        }
    }

    private fun actualizarListaTareas() {
        val tareas = TareaRepository.obtenerTodas()
        binding.textViewInfo.text = "TaskManager — ${tareas.size} tareas"

        val textoLista = tareas.joinToString("\n\n") { tarea ->
            "📋 [${tarea.id}] ${tarea.titulo}\n" +
            "   ${tarea.descripcion}\n" +
            "   Estado: ${tarea.estado.nombre}"
        }
        binding.textViewListaTareas.text = textoLista

        // Hacer cada tarea "clickeable" — por ahora, click en la primera tarea visible
        // Para simplificar sin RecyclerView, agregamos un listener al TextView
        binding.textViewListaTareas.setOnClickListener {
            // Navegar a detalles de la primera tarea como demostración
            val primeraTarea = tareas.firstOrNull()
            primeraTarea?.let { navegarADetalles(it) }
        }
    }

    private fun navegarADetalles(tarea: Tarea) {
        val intent = Intent(this, DetallesTareaActivity::class.java).apply {
            putExtra(DetallesTareaActivity.EXTRA_TAREA_ID, tarea.id)
            putExtra(DetallesTareaActivity.EXTRA_TAREA_TITULO, tarea.titulo)
            putExtra(DetallesTareaActivity.EXTRA_TAREA_DESCRIPCION, tarea.descripcion)
            putExtra(DetallesTareaActivity.EXTRA_TAREA_ESTADO, tarea.estado.nombre)
        }
        startActivity(intent)
    }
}
```

### Resultado Esperado

La aplicación muestra la pantalla principal con la lista de 3 tareas precargadas, un botón "Crear Nueva Tarea" y un botón "Acerca De". Al pulsar cada botón se navega a la Activity correspondiente.

### Verificación

1. Ejecuta la app. Verifica que se muestran las 3 tareas iniciales.
2. Pulsa **"Acerca De"** → se abre `AcercaDeActivity`. Pulsa **"Volver"** → regresa a `MainActivity`.
3. Pulsa sobre la lista de tareas → se abre `DetallesTareaActivity` mostrando los datos de la primera tarea (ID, título, descripción, estado).
4. Pulsa **"Crear Nueva Tarea"** → se abre el formulario. Escribe un título y descripción, pulsa **"Guardar"** → regresa a `MainActivity` con la nueva tarea visible en la lista.
5. En Logcat (filtro `tag:MainActivity_Lifecycle`), confirma que al navegar a otra Activity se producen `onPause` → `onStop`, y al regresar `onRestart` → `onStart` → `onResume`.

---

## Paso 5 — Verificar y Configurar el AndroidManifest.xml

### Objetivo

Revisar que todas las Activities estén correctamente declaradas en el manifiesto y comprender cada atributo.

### Instrucciones

1. Abre `app/src/main/AndroidManifest.xml`.

2. Verifica que su contenido sea similar al siguiente (Android Studio debería haber agregado las Activities automáticamente al crearlas):

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.TaskManager"
        tools:targetApi="37">

        <!-- Activity principal: punto de entrada -->
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Activity de detalles de tarea -->
        <activity
            android:name=".DetallesTareaActivity"
            android:exported="false"
            android:label="Detalles de Tarea" />

        <!-- Activity para crear nueva tarea -->
        <activity
            android:name=".CrearTareaActivity"
            android:exported="false"
            android:label="Crear Tarea" />

        <!-- Activity informativa -->
        <activity
            android:name=".AcercaDeActivity"
            android:exported="false"
            android:label="Acerca De" />

    </application>

</manifest>
```

3. Si alguna Activity falta, agrégala manualmente dentro del bloque `<application>`.

4. Revisa los siguientes atributos clave:

   | Atributo | Significado |
   |---|---|
   | `android:name` | Nombre de la clase de la Activity (`.MainActivity` = paquete + clase) |
   | `android:exported="true"` | Puede ser iniciada por componentes externos (obligatorio para la Activity del LAUNCHER) |
   | `android:exported="false"` | Solo accesible desde dentro de la propia aplicación |
   | `android:label` | Título que aparece en la barra de la Activity |
   | `<intent-filter>` con `MAIN` + `LAUNCHER` | Marca esta Activity como punto de entrada desde el lanzador del sistema |

5. Abre `res/values/strings.xml` y verifica que contenga:

```xml
<resources>
    <string name="app_name">TaskManager</string>
</resources>
```

### Resultado Esperado

El manifiesto declara 4 Activities: `MainActivity` (exported=true con intent-filter), `DetallesTareaActivity`, `CrearTareaActivity` y `AcercaDeActivity` (todas exported=false).

### Verificación

Ejecuta **Build → Rebuild Project**. Si falta alguna declaración de Activity, Android generará un error indicando que la Activity no está registrada en el manifiesto.

---

## Paso 6 — Manejar Estado ante Rotación con onSaveInstanceState

### Objetivo

Implementar `onSaveInstanceState` y `onRestoreInstanceState` en `CrearTareaActivity` para preservar el contenido del formulario cuando el dispositivo rota.

### Instrucciones

1. Primero, **demuestra el problema**. Ejecuta la app, navega a "Crear Nueva Tarea", escribe texto en los campos, y rota el emulador (`Ctrl+←` o `Ctrl+→`). Observa que los campos `TextInputEditText` **pueden** preservar su contenido automáticamente si tienen un `android:id` definido (Android lo hace por defecto para Views con ID). Sin embargo, cualquier variable en memoria se pierde.

2. Para demostrar la pérdida de datos en variables, modifica `CrearTareaActivity.kt` agregando un contador de caracteres que se pierde al rotar:

```kotlin
package com.cursokotlin.android.taskmanager

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import androidx.core.widget.addTextChangedListener
import com.cursokotlin.android.taskmanager.databinding.ActivityCrearTareaBinding

class CrearTareaActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "CrearTarea_Lifecycle"
        const val RESULT_TAREA_TITULO = "result_tarea_titulo"
        const val RESULT_TAREA_DESCRIPCION = "result_tarea_descripcion"

        // Claves para guardar/restaurar estado
        private const val KEY_CONTADOR_CARACTERES = "key_contador_caracteres"
    }

    private lateinit var binding: ActivityCrearTareaBinding

    // Variable en memoria que se pierde al rotar SIN onSaveInstanceState
    private var contadorCaracteres: Int = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityCrearTareaBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Restaurar estado si existe
        if (savedInstanceState != null) {
            contadorCaracteres = savedInstanceState.getInt(KEY_CONTADOR_CARACTERES, 0)
            Log.d(TAG, "Estado restaurado: contadorCaracteres=$contadorCaracteres")
        }

        actualizarContador()

        // Listener para contar caracteres del título
        binding.editTextTitulo.addTextChangedListener { texto ->
            contadorCaracteres = texto?.length ?: 0
            actualizarContador()
        }

        binding.buttonGuardar.setOnClickListener {
            val titulo = binding.editTextTitulo.text.toString().trim()
            val descripcion = binding.editTextDescripcion.text.toString().trim()

            if (titulo.isEmpty()) {
                binding.editTextTitulo.error = "El título es obligatorio"
                return@setOnClickListener
            }

            Log.d(TAG, "Guardando tarea: titulo=$titulo")

            val resultIntent = Intent().apply {
                putExtra(RESULT_TAREA_TITULO, titulo)
                putExtra(RESULT_TAREA_DESCRIPCION, descripcion)
            }
            setResult(Activity.RESULT_OK, resultIntent)
            finish()
        }

        binding.buttonCancelar.setOnClickListener {
            setResult(Activity.RESULT_CANCELED)
            finish()
        }
    }

    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        // Guardar el contador antes de que la Activity sea destruida
        outState.putInt(KEY_CONTADOR_CARACTERES, contadorCaracteres)
        Log.d(TAG, ">>> onSaveInstanceState: contadorCaracteres=$contadorCaracteres guardado")
    }

    override fun onRestoreInstanceState(savedInstanceState: Bundle) {
        super.onRestoreInstanceState(savedInstanceState)
        contadorCaracteres = savedInstanceState.getInt(KEY_CONTADOR_CARACTERES, 0)
        actualizarContador()
        Log.d(TAG, ">>> onRestoreInstanceState: contadorCaracteres=$contadorCaracteres restaurado")
    }

    private fun actualizarContador() {
        // Actualizar el hint del título para mostrar el contador
        binding.editTextTitulo.hint?.let {
            // Usamos el título de la Activity como indicador visual
            title = "Crear Tarea ($contadorCaracteres chars)"
        }
    }
}
```

3. Ejecuta la app, navega a "Crear Nueva Tarea", escribe "Hola" en el título.

4. Observa que el título de la barra muestra "Crear Tarea (4 chars)".

5. Rota el emulador. Verifica en Logcat (filtro `tag:CrearTarea_Lifecycle`):

### Resultado Esperado

```
D  >>> onSaveInstanceState: contadorCaracteres=4 guardado
D  >>> onCreate() llamado
D  Estado restaurado: contadorCaracteres=4
D  >>> onRestoreInstanceState: contadorCaracteres=4 restaurado
```

El texto del `EditText` se preserva automáticamente (por tener `android:id`), y el contador de caracteres se restaura correctamente gracias a `onSaveInstanceState`.

### Verificación

- Tras la rotación, el título de la barra sigue mostrando "Crear Tarea (4 chars)".
- El texto "Hola" sigue presente en el campo de título.
- Los logs confirman la secuencia completa de guardado y restauración.

---

## Paso 7 — Introducir ViewModel con LiveData

### Objetivo

Crear un `TareasViewModel` que mantenga la lista de tareas en un `LiveData`, permitiendo que los datos sobrevivan rotaciones sin necesidad de `onSaveInstanceState` para la lista completa.

### Instrucciones

1. Crea un nuevo paquete llamado `viewmodel` dentro del paquete base.

2. Dentro de `viewmodel`, crea el archivo `TareasViewModel.kt`:

```kotlin
package com.cursokotlin.android.taskmanager.viewmodel

import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel
import com.cursokotlin.android.taskmanager.model.Tarea
import com.cursokotlin.android.taskmanager.repository.TareaRepository

/**
 * ViewModel que gestiona la lista de tareas.
 * Sobrevive a cambios de configuración (rotaciones).
 */
class TareasViewModel : ViewModel() {

    // MutableLiveData privado — solo el ViewModel puede modificarlo
    private val _tareas = MutableLiveData<List<Tarea>>()

    // LiveData público — las Activities solo pueden observarlo
    val tareas: LiveData<List<Tarea>> get() = _tareas

    init {
        // Cargar tareas iniciales del repositorio
        cargarTareas()
    }

    fun cargarTareas() {
        _tareas.value = TareaRepository.obtenerTodas()
    }

    fun agregarTarea(titulo: String, descripcion: String) {
        TareaRepository.agregar(titulo, descripcion)
        cargarTareas() // Actualizar LiveData
    }

    override fun onCleared() {
        super.onCleared()
        // Se llama cuando el ViewModel es destruido definitivamente
        android.util.Log.d("TareasViewModel", ">>> onCleared() — ViewModel destruido")
    }
}
```

3. Ahora actualiza `MainActivity.kt` para usar el `ViewModel` en lugar de acceder directamente al repositorio:

```kotlin
package com.cursokotlin.android.taskmanager

import android.content.Intent
import android.os.Bundle
import android.util.Log
import android.widget.Toast
import androidx.activity.result.ActivityResultLauncher
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import androidx.lifecycle.ViewModelProvider
import com.cursokotlin.android.taskmanager.databinding.ActivityMainBinding
import com.cursokotlin.android.taskmanager.model.Tarea
import com.cursokotlin.android.taskmanager.viewmodel.TareasViewModel

class MainActivity : AppCompatActivity() {

    companion object {
        private const val TAG = "MainActivity_Lifecycle"
    }

    private lateinit var binding: ActivityMainBinding
    private lateinit var crearTareaLauncher: ActivityResultLauncher<Intent>

    // ViewModel — sobrevive rotaciones
    private lateinit var viewModel: TareasViewModel

    // ── Ciclo de vida ──────────────────────────────────────

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, ">>> onCreate() llamado")

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Obtener (o crear) el ViewModel asociado a esta Activity
        viewModel = ViewModelProvider(this)[TareasViewModel::class.java]
        Log.d(TAG, "ViewModel obtenido: ${viewModel.hashCode()}")

        // Observar cambios en la lista de tareas
        viewModel.tareas.observe(this) { listaTareas ->
            Log.d(TAG, "LiveData actualizado: ${listaTareas.size} tareas")
            mostrarTareas(listaTareas)
        }

        registrarLauncher()
        configurarNavegacion()
    }

    override fun onStart() {
        super.onStart()
        Log.d(TAG, ">>> onStart() llamado")
    }

    override fun onResume() {
        super.onResume()
        Log.d(TAG, ">>> onResume() llamado — Activity VISIBLE e INTERACTIVA")
    }

    override fun onPause() {
        super.onPause()
        Log.d(TAG, ">>> onPause() llamado — Activity pierde el foco")
    }

    override fun onStop() {
        super.onStop()
        Log.d(TAG, ">>> onStop() llamado — Activity NO visible")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d(TAG, ">>> onRestart() llamado — Activity regresa de onStop")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, ">>> onDestroy() llamado — Activity DESTRUIDA")
    }

    // ── Métodos privados ───────────────────────────────────

    private fun registrarLauncher() {
        crearTareaLauncher = registerForActivityResult(
            ActivityResultContracts.StartActivityForResult()
        ) { resultado ->
            if (resultado.resultCode == RESULT_OK) {
                val data = resultado.data
                val titulo = data?.getStringExtra(
                    CrearTareaActivity.RESULT_TAREA_TITULO
                ) ?: ""
                val descripcion = data?.getStringExtra(
                    CrearTareaActivity.RESULT_TAREA_DESCRIPCION
                ) ?: ""

                if (titulo.isNotEmpty()) {
                    // Agregar a través del ViewModel (no directamente al repositorio)
                    viewModel.agregarTarea(titulo, descripcion)
                    Toast.makeText(this, "Tarea '$titulo' creada", Toast.LENGTH_SHORT).show()
                }
            } else {
                Log.d(TAG, "Creación de tarea cancelada")
            }
        }
    }

    private fun configurarNavegacion() {
        binding.buttonCrearTarea.setOnClickListener {
            val intent = Intent(this, CrearTareaActivity::class.java)
            crearTareaLauncher.launch(intent)
        }

        binding.buttonAcercaDe.setOnClickListener {
            val intent = Intent(this, AcercaDeActivity::class.java)
            startActivity(intent)
        }
    }

    private fun mostrarTareas(tareas: List<Tarea>) {
        binding.textViewInfo.text = "TaskManager — ${tareas.size} tareas"

        val textoLista = tareas.joinToString("\n\n") { tarea ->
            "📋 [${tarea.id}] ${tarea.titulo}\n" +
            "   ${tarea.descripcion}\n" +
            "   Estado: ${tarea.estado.nombre}"
        }
        binding.textViewListaTareas.text = textoLista

        binding.textViewListaTareas.setOnClickListener {
            val primeraTarea = tareas.firstOrNull()
            primeraTarea?.let { navegarADetalles(it) }
        }
    }

    private fun navegarADetalles(tarea: Tarea) {
        val intent = Intent(this, DetallesTareaActivity::class.java).apply {
            putExtra(DetallesTareaActivity.EXTRA_TAREA_ID, tarea.id)
            putExtra(DetallesTareaActivity.EXTRA_TAREA_TITULO, tarea.titulo)
            putExtra(DetallesTareaActivity.EXTRA_TAREA_DESCRIPCION, tarea.descripcion)
            putExtra(DetallesTareaActivity.EXTRA_TAREA_ESTADO, tarea.estado.nombre)
        }
        startActivity(intent)
    }
}
```

4. Ejecuta la app y crea una nueva tarea (ej. "Tarea de prueba ViewModel").

5. Verifica que la nueva tarea aparece en la lista (ahora gestionada por LiveData).

6. **Rota el dispositivo** (`Ctrl+←` o `Ctrl+→`).

7. Observa en Logcat:

### Resultado Esperado

```
D  >>> onPause() llamado — Activity pierde el foco
D  >>> onStop() llamado — Activity NO visible
D  >>> onDestroy() llamado — Activity DESTRUIDA
D  >>> onCreate() llamado
D  ViewModel obtenido: 123456789    ← MISMO hashCode que antes
D  LiveData actualizado: 4 tareas   ← La lista se mantiene intacta
D  >>> onStart() llamado
D  >>> onResume() llamado — Activity VISIBLE e INTERACTIVA
```

**Punto clave:** El `hashCode` del ViewModel es el mismo antes y después de la rotación. Esto demuestra que el ViewModel **sobrevive** a la destrucción y recreación de la Activity. La lista de 4 tareas (3 precargadas + 1 nueva) se mantiene sin necesidad de guardarla manualmente.

### Verificación

- La lista de tareas se muestra completa después de rotar (incluyendo cualquier tarea creada durante la sesión).
- El log muestra que `onCleared()` del ViewModel **NO** se llama al rotar (solo se llamaría al cerrar la Activity definitivamente con Back).
- Presiona Back para salir completamente. Ahora sí debe aparecer en Logcat: `>>> onCleared() — ViewModel destruido`.

---

## Paso 8 — Prueba Integral de Navegación y Ciclo de Vida

### Objetivo

Ejecutar una secuencia completa de pruebas que valide todos los mecanismos implementados: ciclo de vida, navegación, paso de datos, comunicación bidireccional, preservación de estado y ViewModel.

### Instrucciones

Ejecuta la siguiente secuencia de pruebas en orden, registrando los resultados:

#### Prueba 1: Ciclo de vida básico

1. Inicia la app desde cero.
2. Presiona **Home**.
3. Vuelve a la app desde recientes.
4. Presiona **Back** para salir.

**Resultado esperado en Logcat** (filtro `tag:MainActivity_Lifecycle`):

```
D  >>> onCreate() llamado
D  >>> onStart() llamado
D  >>> onResume() llamado
D  >>> onPause() llamado        ← Home
D  >>> onStop() llamado
D  >>> onRestart() llamado      ← Volver
D  >>> onStart() llamado
D  >>> onResume() llamado
D  >>> onPause() llamado        ← Back
D  >>> onStop() llamado
D  >>> onDestroy() llamado
```

#### Prueba 2: Navegación con Intent explícito y extras

1. Inicia la app.
2. Pulsa sobre la lista de tareas para abrir los detalles de la primera tarea.
3. Verifica que `DetallesTareaActivity` muestra: ID: 1, "Configurar entorno", "Instalar Android Studio y crear AVDs", Estado: Completada.
4. Pulsa "Volver".

#### Prueba 3: Comunicación bidireccional con ActivityResultLauncher

1. Pulsa "Crear Nueva Tarea".
2. Escribe título: "Estudiar ViewModel" y descripción: "Comprender LiveData y observe".
3. Pulsa "Guardar".
4. Verifica que la lista ahora muestra 4 tareas y la nueva aparece al final.
5. Pulsa "Crear Nueva Tarea" de nuevo.
6. Pulsa "Cancelar".
7. Verifica que la lista sigue con 4 tareas (no se agregó ninguna).

#### Prueba 4: Preservación de estado ante rotación

1. Pulsa "Crear Nueva Tarea".
2. Escribe "Test rotación" en el título.
3. Rota el emulador.
4. Verifica que el texto "Test rotación" sigue en el campo.
5. Verifica que el título de la barra muestra el contador correcto.
6. Pulsa "Cancelar" y regresa.

#### Prueba 5: ViewModel sobrevive rotación

1. En la pantalla principal, anota cuántas tareas hay.
2. Rota el emulador.
3. Verifica que la cantidad de tareas es la misma.
4. Verifica en Logcat que el hashCode del ViewModel es idéntico.

#### Prueba 6: Ejecución en AVD API 30

1. Cambia el AVD a uno con **API 30**.
2. Ejecuta la app.
3. Repite las pruebas 2 y 3.
4. Verifica que todo funciona correctamente en la versión mínima soportada.

### Resultado Esperado

Las 6 pruebas se completan exitosamente. La app funciona de manera idéntica en API 30 y API 37.

### Verificación

Todas las pruebas producen los resultados descritos. No hay crashes ni errores en Logcat con nivel `E` (Error) para los TAGs del proyecto.

---

## Validación y Pruebas

Ejecuta las siguientes verificaciones finales:

| # | Verificación | Comando / Acción | Resultado Esperado |
|---|---|---|---|
| 1 | Compilación limpia | **Build → Rebuild Project** | `BUILD SUCCESSFUL` sin warnings críticos |
| 2 | Manifiesto completo | Abrir `AndroidManifest.xml` | 4 Activities declaradas, `MainActivity` con `exported="true"` |
| 3 | Logs de ciclo de vida | Filtrar Logcat por `tag:MainActivity_Lifecycle` | Secuencia completa de callbacks visible |
| 4 | Navegación a Detalles | Pulsar lista de tareas | `DetallesTareaActivity` muestra datos correctos vía extras |
| 5 | Crear tarea con resultado | Crear y guardar tarea | Lista actualizada con nueva tarea |
| 6 | Cancelar creación | Pulsar "Cancelar" en formulario | Lista sin cambios |
| 7 | Estado ante rotación | Rotar en `CrearTareaActivity` | Texto y contador preservados |
| 8 | ViewModel persistente | Rotar en `MainActivity` | Mismo hashCode, misma lista |
| 9 | API 30 compatible | Ejecutar en AVD API 30 | Funcionalidad idéntica |
| 10 | ViewBinding activo | Verificar `buildFeatures { viewBinding = true }` | Sin `findViewById` en el código |

---

## Solución de Problemas

### Problema 1: Error "Activity not found" al navegar

**Síntomas:** La app se cierra con un `ActivityNotFoundException` al pulsar un botón de navegación. En Logcat aparece:

```
E  android.content.ActivityNotFoundException: Unable to find explicit activity class
   {com.cursokotlin.android.taskmanager/com.cursokotlin.android.taskmanager.DetallesTareaActivity}
```

**Causa:** La Activity no está declarada en `AndroidManifest.xml`. Esto ocurre si se creó la clase Kotlin manualmente en lugar de usar **New → Activity → Empty Views Activity**, que registra automáticamente la Activity en el manifiesto.

**Solución:**

1. Abre `app/src/main/AndroidManifest.xml`.
2. Verifica que dentro de `<application>` exista un bloque `<activity>` para cada Activity:

```xml
<activity
    android:name=".DetallesTareaActivity"
    android:exported="false" />
```

3. Agrega manualmente cualquier declaración faltante.
4. Ejecuta **Build → Rebuild Project** y vuelve a lanzar la app.

---

### Problema 2: ViewModel se recrea al rotar (hashCode diferente)

**Síntomas:** Después de rotar el dispositivo, el log muestra un `hashCode` diferente para el ViewModel, y la lista de tareas vuelve a tener solo las 3 tareas precargadas (se pierden las creadas durante la sesión).

**Causa:** El ViewModel se está instanciando directamente con el constructor (`TareasViewModel()`) en lugar de obtenerlo a través de `ViewModelProvider`. Solo `ViewModelProvider` asocia el ViewModel al ciclo de vida del `ViewModelStoreOwner` (la Activity), permitiéndole sobrevivir cambios de configuración.

**Solución:**

1. Verifica que en `MainActivity.kt` la inicialización del ViewModel sea exactamente:

```kotlin
viewModel = ViewModelProvider(this)[TareasViewModel::class.java]
```

2. **NO** uses:

```kotlin
// ❌ INCORRECTO — crea una instancia nueva cada vez
viewModel = TareasViewModel()
```

3. Asegúrate de que la clase `TareasViewModel` extiende de `ViewModel()` (no de `AppCompatActivity` ni de ninguna otra clase).
4. Reconstruye y ejecuta. El hashCode debe ser idéntico antes y después de la rotación.

---

## Limpieza

Al finalizar el laboratorio, el proyecto debe mantenerse intacto para los laboratorios posteriores (Lab 4 y Lab 5 construyen sobre TaskManager). Sin embargo, realiza las siguientes acciones de mantenimiento:

1. **Cerrar AVDs innecesarios:** Si tienes múltiples emuladores abiertos, cierra los que no estés usando para liberar RAM.

2. **Limpiar caché de compilación (opcional):** Si experimentaste problemas de compilación durante el laboratorio:

   ```
   Build → Clean Project
   ```

3. **Verificar control de versiones:** Si usas Git, este es un buen momento para hacer commit:

   ```bash
   cd ~/AndroidCursoBasico/Lab3/TaskManager
   git init
   git add .
   git commit -m "Lab 3 completo: navegación, ciclo de vida, ViewModel"
   ```

4. **No eliminar el proyecto.** Se reutilizará en el Laboratorio 4 (TaskManagerUI) y Laboratorio 5 (TaskManagerMedia).

---

## Resumen

En este laboratorio implementaste los fundamentos de la navegación y el ciclo de vida en Android:

| Concepto | Implementación |
|---|---|
| **Ciclo de vida** | 7 callbacks instrumentados con `Log.d` en `MainActivity`, verificados mediante rotación, Home y Back |
| **Navegación con Intents** | 3 Activities adicionales conectadas con `Intent(this, Target::class.java)` |
| **Paso de datos** | `putExtra` / `getStringExtra` / `getIntExtra` con claves en `companion object` |
| **Comunicación bidireccional** | `ActivityResultLauncher` con `registerForActivityResult` para recibir la tarea creada |
| **Preservación de estado** | `onSaveInstanceState` / `onRestoreInstanceState` para el contador de caracteres en el formulario |
| **ViewModel + LiveData** | `TareasViewModel` con `MutableLiveData<List<Tarea>>` observado desde `MainActivity` |
| **AndroidManifest.xml** | 4 Activities declaradas con atributos `name`, `exported`, `label` e `intent-filter` |

### Estructura Final del Proyecto

```
app/src/main/kotlin/com/cursokotlin/android/taskmanager/
├── MainActivity.kt
├── DetallesTareaActivity.kt
├── CrearTareaActivity.kt
├── AcercaDeActivity.kt
├── model/
│   └── Tarea.kt              (data class Tarea + sealed class EstadoTarea)
├── repository/
│   └── TareaRepository.kt    (repositorio en memoria singleton)
└── viewmodel/
    └── TareasViewModel.kt     (ViewModel con LiveData)
```

### Recursos Adicionales

- [Guía oficial: Ciclo de vida de una Activity](https://developer.android.com/guide/components/activities/activity-lifecycle)
- [Guía oficial: Intents y filtros de intents](https://developer.android.com/guide/components/intents-filters)
- [Guía oficial: ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel)
- [Guía oficial: LiveData](https://developer.android.com/topic/libraries/architecture/livedata)
- [Referencia: ActivityResultLauncher](https://developer.android.com/training/basics/intents/result)

---
