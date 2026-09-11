# Laboratorio 5 — Aplicación Multimedia con Cámara, Galería y Reproducción de Medios

## 1. Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 216 minutos (aprox. 3 h 36 min) |
| **Complejidad** | Alta |
| **Nivel Bloom** | Crear |

---

## 2. Descripción General

En este laboratorio final del curso básico construirás **TaskManagerMedia**, una extensión de la aplicación TaskManagerUI del Laboratorio 4 que integra funcionalidades multimedia completas. Implementarás el modelo de permisos en tiempo de ejecución para cámara y almacenamiento, capturarás fotografías con la cámara del dispositivo, seleccionarás imágenes de la galería, reproducirás audio y video desde recursos locales, e integrarás todo en un flujo coherente donde las tareas pueden tener imágenes adjuntas y sonidos de notificación al completarse. Como cierre, implementarás detección básica de agitación del dispositivo usando el acelerómetro.

---

## 3. Objetivos de Aprendizaje

Al completar este laboratorio serás capaz de:

- [ ] Implementar el modelo de permisos en tiempo de ejecución usando `ActivityResultContracts.RequestMultiplePermissions` para cámara y almacenamiento, manejando los flujos de concesión, rationale y denegación permanente.
- [ ] Capturar fotografías con la cámara del dispositivo mediante `ActivityResultContracts.TakePicture()` con `FileProvider` y seleccionar imágenes de la galería con `ActivityResultContracts.GetContent()`, mostrándolas eficientemente en `ImageView` usando `BitmapFactory` con submuestreo y Glide.
- [ ] Implementar reproducción de audio con `MediaPlayer` (play/pause/stop) y video con `VideoView` + `MediaController`, manejando correctamente el ciclo de vida de ambos componentes.
- [ ] Integrar todas las funcionalidades multimedia en la aplicación TaskManagerMedia: adjuntar imágenes a tareas, mostrar miniaturas en el RecyclerView y reproducir sonido de notificación al completar una tarea.
- [ ] Detectar agitación del dispositivo mediante `SensorManager` y `SensorEventListener` del acelerómetro para mostrar un `Toast` informativo.

---

## 4. Prerrequisitos

### Conocimientos Previos

| Requisito | Fuente |
|---|---|
| Laboratorio 4 completado: TaskManagerUI con RecyclerView, View Binding, navegación entre 4 Activities | Lab 04 |
| View Binding, RecyclerView Adapter, ViewModel con LiveData | Lab 04 |
| Sistema de permisos de Android y AndroidManifest.xml | Lab 03 |
| Modelo de datos `Tarea` con propiedades extensibles | Lab 02 |
| Kotlin básico-intermedio: data classes, funciones de extensión, lambdas | Labs 01-04 |

### Acceso Requerido

| Recurso | Detalle |
|---|---|
| Android Studio Quail 3 (2026.1.3 Patch 1) | Instalado y funcional |
| AVDs configurados | API 30, 35, 36 y 37 con **cámara habilitada** en la configuración del emulador |
| Conexión a Internet | Para descarga de dependencias Gradle y Glide |
| Archivos multimedia de prueba | Se proporcionan instrucciones para obtener/crear `notification.mp3` e `intro.mp4` |

---

## 5. Entorno del Laboratorio

### Hardware Mínimo

| Componente | Especificación |
|---|---|
| Procesador | Intel Core i5 8ª gen. o AMD Ryzen 5 (64-bit, VT-x/AMD-V habilitado) |
| RAM | 16 GB mínimo (32 GB recomendado) |
| Disco | 50 GB libres en SSD |
| GPU | Compatible con OpenGL ES 2.0+ |
| Pantalla | 1280×800 mínimo (1920×1080 recomendado) |

### Software Requerido

| Software | Versión |
|---|---|
| Android Studio | Quail 3 — 2026.1.3 Patch 1 |
| Kotlin | 2.2.10 |
| AGP | 9.3.2 |
| Gradle | 9.3.2 |
| JDK | 11 (JavaVersion.VERSION_11) |
| compileSdk / targetSdk | 37 |
| minSdk | 30 |
| Compose BOM | 2026.02.01 |
| Glide | 4.16.0 |

### Estructura de Directorios del Proyecto

```
C:\AndroidCursoBasico\Lab5\TaskManagerMedia\   (Windows)
~/AndroidCursoBasico/Lab5/TaskManagerMedia/     (macOS/Linux)
```

### Configuración Inicial — Verificar Cámara en AVD

Antes de comenzar, verifica que tus AVDs tengan la cámara habilitada:

1. Abre **Android Studio → Device Manager**.
2. Selecciona tu AVD (API 37 recomendado para desarrollo) → clic en el icono de **lápiz** (Edit).
3. Clic en **Show Advanced Settings**.
4. En la sección **Camera**, configura:
   - **Front:** `Emulated` o `Webcam0`
   - **Back:** `Emulated` o `Webcam0`
5. Clic en **Finish**.
6. Repite para los AVDs de API 30, 35 y 36.

---

## 6. Instrucciones Paso a Paso

### Paso 1 — Crear el Proyecto TaskManagerMedia

**Objetivo:** Crear el proyecto base con la plantilla correcta y configurar todas las dependencias necesarias.

> **Nota:** Si deseas partir del código del Laboratorio 4, puedes copiar el proyecto TaskManagerUI y renombrarlo. Las instrucciones a continuación crean el proyecto desde cero para garantizar una configuración limpia.

**Instrucciones:**

1. Abre **Android Studio Quail 3**.

2. Selecciona **File → New → New Project**.

3. Elige la plantilla **Empty Views Activity** (NO "Empty Activity").

4. Configura el proyecto:
   - **Name:** `TaskManagerMedia`
   - **Package name:** `com.cursokotlin.android.taskmanagermedia`
   - **Save location:** `C:\AndroidCursoBasico\Lab5\TaskManagerMedia` (Windows) o `~/AndroidCursoBasico/Lab5/TaskManagerMedia` (macOS/Linux)
   - **Language:** Kotlin
   - **Minimum SDK:** API 30: Android 11.0 (R)
   - **Build configuration language:** Kotlin DSL (build.gradle.kts)

5. Clic en **Finish** y espera a que Gradle sincronice.

6. Abre el archivo `gradle/libs.versions.toml` y reemplaza su contenido completo con:

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
recyclerview = "1.4.0"
glide = "4.16.0"
lifecycleLivedataKtx = "2.6.1"
lifecycleViewmodelKtx = "2.6.1"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-lifecycle-livedata-ktx = { group = "androidx.lifecycle", name = "lifecycle-livedata-ktx", version.ref = "lifecycleLivedataKtx" }
androidx-lifecycle-viewmodel-ktx = { group = "androidx.lifecycle", name = "lifecycle-viewmodel-ktx", version.ref = "lifecycleViewmodelKtx" }
androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
androidx-recyclerview = { group = "androidx.recyclerview", name = "recyclerview", version.ref = "recyclerview" }
glide = { group = "com.github.bumptech.glide", name = "glide", version.ref = "glide" }
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitExt" }
androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

7. Abre `app/build.gradle.kts` y reemplaza su contenido con:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "com.cursokotlin.android.taskmanagermedia"
    compileSdk = 37

    defaultConfig {
        applicationId = "com.cursokotlin.android.taskmanagermedia"
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
    implementation(libs.androidx.recyclerview)
    implementation(libs.androidx.lifecycle.runtime.ktx)
    implementation(libs.androidx.lifecycle.livedata.ktx)
    implementation(libs.androidx.lifecycle.viewmodel.ktx)
    implementation(libs.glide)

    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
}
```

8. Verifica que `settings.gradle.kts` contenga los repositorios necesarios:

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
        gradlePluginPortal()
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "TaskManagerMedia"
include(":app")
```

9. Sincroniza Gradle: **File → Sync Project with Gradle Files**.

**Resultado Esperado:**

La sincronización de Gradle completa sin errores. El proyecto aparece en el panel Project con la estructura estándar.

**Verificación:**

- En la ventana **Build**, confirma el mensaje `BUILD SUCCESSFUL`.
- En el panel **Project**, verifica que existe `app/build.gradle.kts` con `compileSdk = 37`.

---

### Paso 2 — Preparar Archivos Multimedia y Recursos

**Objetivo:** Agregar los archivos de audio, video e imágenes placeholder necesarios para el laboratorio.

**Instrucciones:**

1. Crea la carpeta `res/raw` si no existe:
   - Clic derecho sobre `app/src/main/res` → **New → Android Resource Directory**.
   - **Resource type:** `raw` → **OK**.

2. **Archivo de audio** — Necesitas un archivo `notification.mp3` en `res/raw/`. Tienes dos opciones:

   **Opción A (recomendada):** Descarga un sonido de notificación libre de derechos desde [freesound.org](https://freesound.org) o [pixabay.com/sound-effects](https://pixabay.com/sound-effects/). Renómbralo a `notification.mp3`.

   **Opción B:** Usa el tono de notificación del sistema. Crea un archivo vacío temporal — lo reemplazarás después con un archivo real.

   Copia el archivo `notification.mp3` a `app/src/main/res/raw/`.

3. **Archivo de video** — Necesitas un archivo `intro.mp4` en `res/raw/`. Opciones:

   **Opción A (recomendada):** Descarga un video corto (5-15 segundos, máximo 5 MB) desde [pexels.com/videos](https://www.pexels.com/videos/) o [pixabay.com/videos](https://pixabay.com/videos/). Renómbralo a `intro.mp4`.

   **Opción B:** Graba un video corto con tu teléfono o usa cualquier MP4 de prueba.

   Copia el archivo `intro.mp4` a `app/src/main/res/raw/`.

   > **Importante:** Los nombres de archivos en `res/raw` deben ser minúsculas, sin espacios ni caracteres especiales. Solo letras, números y guiones bajos.

4. **Imágenes placeholder** — Crea dos imágenes vectoriales drawable:

   Clic derecho sobre `res/drawable` → **New → Vector Asset**:

   - **Asset type:** Clip Art
   - **Name:** `ic_placeholder`
   - **Clip Art:** busca "image" → selecciona el icono de imagen (landscape)
   - **Size:** 48dp × 48dp
   - **Color:** `#FFBDBDBD` (gris claro)
   - Clic en **Next** → **Finish**.

   Repite para crear un segundo vector asset:

   - **Asset type:** Clip Art
   - **Name:** `ic_camera`
   - **Clip Art:** busca "photo camera" → selecciona el icono de cámara
   - **Size:** 48dp × 48dp
   - **Color:** `#FF757575` (gris medio)
   - Clic en **Next** → **Finish**.

5. **Imagen de error para Glide** — Crea un tercer vector asset:

   - **Asset type:** Clip Art
   - **Name:** `ic_broken_image`
   - **Clip Art:** busca "broken image" → selecciona el icono de imagen rota
   - **Size:** 48dp × 48dp
   - **Color:** `#FFE57373` (rojo claro)
   - Clic en **Next** → **Finish**.

6. Verifica que los archivos existen en las rutas correctas:

```
app/src/main/res/raw/notification.mp3
app/src/main/res/raw/intro.mp4
app/src/main/res/drawable/ic_placeholder.xml
app/src/main/res/drawable/ic_camera.xml
app/src/main/res/drawable/ic_broken_image.xml
```

**Resultado Esperado:**

Los archivos multimedia están en `res/raw/` y los drawables vectoriales en `res/drawable/`. No hay errores de compilación.

**Verificación:**

- En el panel **Project** (vista Android), expande `res/raw` y confirma que aparecen `notification` e `intro`.
- Expande `res/drawable` y confirma que aparecen `ic_placeholder`, `ic_camera` e `ic_broken_image`.

---

### Paso 3 — Configurar AndroidManifest.xml con Permisos y FileProvider

**Objetivo:** Declarar los permisos necesarios y configurar el `FileProvider` para compartir URIs de archivos de forma segura con la cámara.

**Instrucciones:**

1. Abre `app/src/main/AndroidManifest.xml` y reemplaza su contenido completo con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- Permiso de cámara -->
    <uses-permission android:name="android.permission.CAMERA" />

    <!-- Permisos de almacenamiento para API 29-32 -->
    <uses-permission
        android:name="android.permission.READ_EXTERNAL_STORAGE"
        android:maxSdkVersion="32" />
    <uses-permission
        android:name="android.permission.WRITE_EXTERNAL_STORAGE"
        android:maxSdkVersion="29"
        tools:ignore="ScopedStorage" />

    <!-- Permiso de imágenes para API 33+ -->
    <uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />

    <!-- Declarar que la app usa cámara pero no es obligatoria -->
    <uses-feature
        android:name="android.hardware.camera"
        android:required="false" />
    <uses-feature
        android:name="android.hardware.camera.autofocus"
        android:required="false" />

    <!-- Declarar uso del acelerómetro -->
    <uses-feature
        android:name="android.hardware.sensor.accelerometer"
        android:required="false" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.TaskManagerMedia">

        <!-- FileProvider para compartir URIs de fotos con la cámara -->
        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="com.cursokotlin.android.taskmanagermedia.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths" />
        </provider>

        <activity
            android:name=".ui.MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity android:name=".ui.AddTaskActivity" />
        <activity android:name=".ui.TaskDetailActivity" />
        <activity android:name=".ui.MediaPlayerActivity" />

    </application>

</manifest>
```

2. Crea el archivo de rutas para `FileProvider`. Crea la carpeta `res/xml` si no existe:
   - Clic derecho sobre `app/src/main/res` → **New → Android Resource Directory**.
   - **Resource type:** `xml` → **OK**.

3. Crea el archivo `file_paths.xml`:
   - Clic derecho sobre `res/xml` → **New → XML Resource File**.
   - **File name:** `file_paths` → **OK**.

4. Reemplaza el contenido de `res/xml/file_paths.xml` con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <!-- Directorio de imágenes capturadas por la cámara -->
    <files-path
        name="task_images"
        path="images/" />

    <!-- Directorio de caché para imágenes temporales -->
    <cache-path
        name="task_images_cache"
        path="images/" />
</paths>
```

5. Abre `res/values/strings.xml` y reemplaza su contenido con:

```xml
<resources>
    <string name="app_name">TaskManagerMedia</string>

    <!-- MainActivity -->
    <string name="title_task_list">Mis Tareas</string>
    <string name="btn_add_task">Agregar Tarea</string>
    <string name="btn_media_player">Reproductor</string>
    <string name="empty_task_list">No hay tareas. ¡Agrega una!</string>

    <!-- AddTaskActivity -->
    <string name="title_add_task">Nueva Tarea</string>
    <string name="hint_task_title">Título de la tarea</string>
    <string name="hint_task_description">Descripción (opcional)</string>
    <string name="btn_take_photo">Tomar Foto</string>
    <string name="btn_pick_gallery">Galería</string>
    <string name="btn_save_task">Guardar Tarea</string>
    <string name="label_attached_image">Imagen adjunta:</string>

    <!-- TaskDetailActivity -->
    <string name="title_task_detail">Detalle de Tarea</string>
    <string name="btn_mark_complete">Marcar Completada</string>
    <string name="btn_delete_task">Eliminar</string>
    <string name="task_completed_message">¡Tarea completada!</string>
    <string name="task_deleted_message">Tarea eliminada</string>

    <!-- MediaPlayerActivity -->
    <string name="title_media_player">Reproductor Multimedia</string>
    <string name="btn_play">▶ Play</string>
    <string name="btn_pause">⏸ Pause</string>
    <string name="btn_stop">⏹ Stop</string>
    <string name="label_audio">Audio de Notificación</string>
    <string name="label_video">Video de Introducción</string>

    <!-- Permisos -->
    <string name="permission_camera_rationale">La cámara es necesaria para tomar fotos de las tareas.</string>
    <string name="permission_storage_rationale">El acceso a almacenamiento es necesario para seleccionar imágenes.</string>
    <string name="permission_denied_permanent">Permiso denegado. Ve a Configuración de la app para habilitarlo manualmente.</string>
    <string name="permission_granted">Permiso concedido</string>

    <!-- Sensor -->
    <string name="shake_detected">¡Agitación detectada! 🎲</string>
    <string name="sensor_not_available">Acelerómetro no disponible en este dispositivo</string>
</resources>
```

6. Abre `res/values/themes.xml` y asegúrate de que el tema exista. Si la plantilla generó un tema diferente, reemplaza con:

```xml
<resources>
    <style name="Theme.TaskManagerMedia" parent="Theme.MaterialComponents.Light.DarkActionBar">
        <item name="colorPrimary">@color/purple_500</item>
        <item name="colorPrimaryVariant">@color/purple_700</item>
        <item name="colorOnPrimary">@color/white</item>
        <item name="colorSecondary">@color/teal_200</item>
        <item name="colorSecondaryVariant">@color/teal_700</item>
        <item name="colorOnSecondary">@color/black</item>
    </style>
</resources>
```

7. Verifica que `res/values/colors.xml` contenga los colores necesarios:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="purple_200">#FFBB86FC</color>
    <color name="purple_500">#FF6200EE</color>
    <color name="purple_700">#FF3700B3</color>
    <color name="teal_200">#FF03DAC5</color>
    <color name="teal_700">#FF018786</color>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="task_complete_bg">#FFE8F5E9</color>
    <color name="task_pending_bg">#FFFFFFFF</color>
    <color name="text_secondary">#FF757575</color>
</resources>
```

**Resultado Esperado:**

El manifiesto declara permisos de cámara y almacenamiento, configura el `FileProvider` con rutas válidas, y declara las 4 Activities del proyecto.

**Verificación:**

- Compila el proyecto con **Build → Make Project** (Ctrl+F9). No debe haber errores.
- Verifica que `res/xml/file_paths.xml` existe y no tiene errores de XML.

---

### Paso 4 — Crear el Modelo de Datos y el Repositorio

**Objetivo:** Definir la data class `Tarea` con soporte para imágenes adjuntas y crear un repositorio en memoria con datos de ejemplo.

**Instrucciones:**

1. Crea la estructura de paquetes. Clic derecho sobre `com.cursokotlin.android.taskmanagermedia` → **New → Package** y crea los siguientes paquetes (uno a uno):
   - `model`
   - `repository`
   - `viewmodel`
   - `ui`
   - `adapter`
   - `util`

2. Crea el modelo de datos. Clic derecho sobre el paquete `model` → **New → Kotlin Class/File** → **Data Class** → nombre: `Tarea`.

3. Reemplaza el contenido de `Tarea.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.model

import android.net.Uri

data class Tarea(
    val id: Int,
    var titulo: String,
    var descripcion: String = "",
    var completada: Boolean = false,
    var imagenUri: Uri? = null,
    val fechaCreacion: Long = System.currentTimeMillis()
) {
    /**
     * Verifica si la tarea tiene una imagen adjunta válida.
     */
    fun tieneImagen(): Boolean = imagenUri != null

    /**
     * Retorna una representación corta de la descripción (máximo 50 caracteres).
     */
    fun descripcionCorta(): String {
        return if (descripcion.length > 50) {
            descripcion.substring(0, 50) + "..."
        } else {
            descripcion
        }
    }

    /**
     * Retorna el estado como texto legible.
     */
    fun estadoTexto(): String = if (completada) "Completada ✓" else "Pendiente"
}
```

4. Crea el repositorio. Clic derecho sobre el paquete `repository` → **New → Kotlin Class/File** → **Object** → nombre: `TareaRepository`.

5. Reemplaza el contenido de `TareaRepository.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.repository

import com.cursokotlin.android.taskmanagermedia.model.Tarea

/**
 * Repositorio singleton que gestiona las tareas en memoria.
 * En una app real, esto se reemplazaría con Room o una API remota.
 */
object TareaRepository {

    private val tareas = mutableListOf<Tarea>()
    private var nextId = 1

    init {
        // Datos de ejemplo
        agregarTarea("Comprar materiales", "Ir a la tienda por materiales del proyecto")
        agregarTarea("Estudiar Kotlin", "Repasar coroutines y flows")
        agregarTarea("Revisar código", "Code review del PR #42")
        agregarTarea("Preparar presentación", "Slides para la reunión del lunes")
    }

    fun obtenerTodas(): List<Tarea> = tareas.toList()

    fun obtenerPorId(id: Int): Tarea? = tareas.find { it.id == id }

    fun agregarTarea(titulo: String, descripcion: String = ""): Tarea {
        val tarea = Tarea(
            id = nextId++,
            titulo = titulo,
            descripcion = descripcion
        )
        tareas.add(tarea)
        return tarea
    }

    fun agregarTareaCompleta(tarea: Tarea): Tarea {
        val nuevaTarea = tarea.copy(id = nextId++)
        tareas.add(nuevaTarea)
        return nuevaTarea
    }

    fun actualizarTarea(tarea: Tarea): Boolean {
        val index = tareas.indexOfFirst { it.id == tarea.id }
        return if (index != -1) {
            tareas[index] = tarea
            true
        } else {
            false
        }
    }

    fun eliminarTarea(id: Int): Boolean {
        return tareas.removeAll { it.id == id }
    }

    fun marcarCompletada(id: Int): Boolean {
        val tarea = obtenerPorId(id) ?: return false
        tarea.completada = true
        return actualizarTarea(tarea)
    }

    fun contarTareas(): Int = tareas.size

    fun contarCompletadas(): Int = tareas.count { it.completada }

    fun contarPendientes(): Int = tareas.count { !it.completada }
}
```

**Resultado Esperado:**

El modelo `Tarea` incluye un campo `imagenUri` opcional para imágenes adjuntas. El repositorio singleton proporciona operaciones CRUD con datos de ejemplo.

**Verificación:**

- Compila el proyecto: **Build → Make Project**. No debe haber errores.
- Verifica que las importaciones de `android.net.Uri` se resolvieron correctamente.

---

### Paso 5 — Crear el ViewModel

**Objetivo:** Implementar el ViewModel con LiveData para gestionar el estado de las tareas de forma reactiva.

**Instrucciones:**

1. Clic derecho sobre el paquete `viewmodel` → **New → Kotlin Class/File** → **Class** → nombre: `TareaViewModel`.

2. Reemplaza el contenido de `TareaViewModel.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.viewmodel

import android.net.Uri
import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel
import com.cursokotlin.android.taskmanagermedia.model.Tarea
import com.cursokotlin.android.taskmanagermedia.repository.TareaRepository

class TareaViewModel : ViewModel() {

    private val _tareas = MutableLiveData<List<Tarea>>()
    val tareas: LiveData<List<Tarea>> get() = _tareas

    private val _tareaSeleccionada = MutableLiveData<Tarea?>()
    val tareaSeleccionada: LiveData<Tarea?> get() = _tareaSeleccionada

    private val _mensaje = MutableLiveData<String?>()
    val mensaje: LiveData<String?> get() = _mensaje

    init {
        cargarTareas()
    }

    fun cargarTareas() {
        _tareas.value = TareaRepository.obtenerTodas()
    }

    fun agregarTarea(titulo: String, descripcion: String, imagenUri: Uri? = null) {
        if (titulo.isBlank()) {
            _mensaje.value = "El título no puede estar vacío"
            return
        }

        val tarea = Tarea(
            id = 0, // El repositorio asignará el ID
            titulo = titulo.trim(),
            descripcion = descripcion.trim(),
            imagenUri = imagenUri
        )
        TareaRepository.agregarTareaCompleta(tarea)
        cargarTareas()
        _mensaje.value = "Tarea agregada exitosamente"
    }

    fun seleccionarTarea(id: Int) {
        _tareaSeleccionada.value = TareaRepository.obtenerPorId(id)
    }

    fun marcarCompletada(id: Int) {
        TareaRepository.marcarCompletada(id)
        cargarTareas()
        _tareaSeleccionada.value = TareaRepository.obtenerPorId(id)
        _mensaje.value = "¡Tarea completada!"
    }

    fun eliminarTarea(id: Int) {
        TareaRepository.eliminarTarea(id)
        cargarTareas()
        _mensaje.value = "Tarea eliminada"
    }

    fun limpiarMensaje() {
        _mensaje.value = null
    }

    fun limpiarSeleccion() {
        _tareaSeleccionada.value = null
    }
}
```

**Resultado Esperado:**

El `TareaViewModel` expone LiveData para la lista de tareas, la tarea seleccionada y mensajes de feedback al usuario.

**Verificación:**

- Compila el proyecto: **Build → Make Project**. No debe haber errores.

---

### Paso 6 — Crear la Utilidad de Permisos

**Objetivo:** Crear una clase utilitaria que centralice la lógica de solicitud y verificación de permisos en tiempo de ejecución.

**Instrucciones:**

1. Clic derecho sobre el paquete `util` → **New → Kotlin Class/File** → **Object** → nombre: `PermissionHelper`.

2. Reemplaza el contenido de `PermissionHelper.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.util

import android.Manifest
import android.content.Context
import android.content.Intent
import android.content.pm.PackageManager
import android.net.Uri
import android.os.Build
import android.provider.Settings
import androidx.activity.result.ActivityResultLauncher
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity
import androidx.core.content.ContextCompat

/**
 * Helper centralizado para gestionar permisos en tiempo de ejecución.
 */
object PermissionHelper {

    /**
     * Retorna los permisos de cámara necesarios.
     */
    fun getCameraPermissions(): Array<String> {
        return arrayOf(Manifest.permission.CAMERA)
    }

    /**
     * Retorna los permisos de almacenamiento/galería según la versión de API.
     */
    fun getGalleryPermissions(): Array<String> {
        return if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
            // API 33+: permiso granular de imágenes
            arrayOf(Manifest.permission.READ_MEDIA_IMAGES)
        } else {
            // API 30-32: permiso de lectura de almacenamiento externo
            arrayOf(Manifest.permission.READ_EXTERNAL_STORAGE)
        }
    }

    /**
     * Verifica si todos los permisos del array están concedidos.
     */
    fun arePermissionsGranted(context: Context, permissions: Array<String>): Boolean {
        return permissions.all { permission ->
            ContextCompat.checkSelfPermission(context, permission) ==
                PackageManager.PERMISSION_GRANTED
        }
    }

    /**
     * Verifica si se debe mostrar rationale para alguno de los permisos.
     */
    fun shouldShowRationale(
        activity: AppCompatActivity,
        permissions: Array<String>
    ): Boolean {
        return permissions.any { permission ->
            activity.shouldShowRequestPermissionRationale(permission)
        }
    }

    /**
     * Muestra un diálogo de rationale explicando por qué se necesita el permiso.
     * Si el usuario acepta, solicita el permiso. Si cancela, no hace nada.
     */
    fun showRationaleDialog(
        activity: AppCompatActivity,
        message: String,
        launcher: ActivityResultLauncher<Array<String>>,
        permissions: Array<String>
    ) {
        AlertDialog.Builder(activity)
            .setTitle("Permiso necesario")
            .setMessage(message)
            .setPositiveButton("Conceder") { _, _ ->
                launcher.launch(permissions)
            }
            .setNegativeButton("Cancelar", null)
            .show()
    }

    /**
     * Muestra un diálogo indicando que el permiso fue denegado permanentemente
     * y ofrece abrir la configuración de la app.
     */
    fun showSettingsDialog(activity: AppCompatActivity, message: String) {
        AlertDialog.Builder(activity)
            .setTitle("Permiso denegado")
            .setMessage(message)
            .setPositiveButton("Ir a Configuración") { _, _ ->
                val intent = Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS).apply {
                    data = Uri.fromParts("package", activity.packageName, null)
                }
                activity.startActivity(intent)
            }
            .setNegativeButton("Cancelar", null)
            .show()
    }

    /**
     * Flujo completo de solicitud de permisos:
     * 1. Si ya están concedidos → ejecuta onGranted
     * 2. Si se debe mostrar rationale → muestra diálogo explicativo
     * 3. Si no → solicita directamente
     */
    fun requestPermissions(
        activity: AppCompatActivity,
        permissions: Array<String>,
        rationaleMessage: String,
        launcher: ActivityResultLauncher<Array<String>>,
        onGranted: () -> Unit
    ) {
        when {
            arePermissionsGranted(activity, permissions) -> {
                onGranted()
            }
            shouldShowRationale(activity, permissions) -> {
                showRationaleDialog(activity, rationaleMessage, launcher, permissions)
            }
            else -> {
                launcher.launch(permissions)
            }
        }
    }
}
```

**Resultado Esperado:**

La clase `PermissionHelper` centraliza toda la lógica de permisos: verificación, rationale, solicitud y manejo de denegación permanente.

**Verificación:**

- Compila el proyecto: **Build → Make Project**. No debe haber errores.
- Verifica que las importaciones de `android.Manifest`, `Build.VERSION_CODES` y `ActivityResultLauncher` se resolvieron correctamente.

---

### Paso 7 — Crear la Utilidad de Imágenes

**Objetivo:** Crear una clase utilitaria para la gestión eficiente de imágenes: creación de archivos temporales, decodificación con submuestreo y carga con Glide.

**Instrucciones:**

1. Clic derecho sobre el paquete `util` → **New → Kotlin Class/File** → **Object** → nombre: `ImageHelper`.

2. Reemplaza el contenido de `ImageHelper.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.util

import android.content.Context
import android.graphics.Bitmap
import android.graphics.BitmapFactory
import android.net.Uri
import android.widget.ImageView
import androidx.core.content.FileProvider
import com.bumptech.glide.Glide
import com.bumptech.glide.load.resource.drawable.DrawableTransitionOptions
import com.cursokotlin.android.taskmanagermedia.R
import java.io.File
import java.io.IOException
import java.text.SimpleDateFormat
import java.util.Date
import java.util.Locale

/**
 * Helper para gestión eficiente de imágenes.
 */
object ImageHelper {

    private const val AUTHORITY = "com.cursokotlin.android.taskmanagermedia.fileprovider"

    /**
     * Crea un archivo temporal para almacenar la foto de la cámara.
     * Retorna un Pair con el File y su Uri de contenido (content://).
     *
     * @throws IOException si no se puede crear el archivo.
     */
    @Throws(IOException::class)
    fun createImageFile(context: Context): Pair<File, Uri> {
        val timeStamp = SimpleDateFormat("yyyyMMdd_HHmmss", Locale.getDefault()).format(Date())
        val fileName = "TASK_IMG_${timeStamp}"

        // Crear directorio si no existe
        val storageDir = File(context.filesDir, "images")
        if (!storageDir.exists()) {
            storageDir.mkdirs()
        }

        val imageFile = File.createTempFile(fileName, ".jpg", storageDir)

        val imageUri = FileProvider.getUriForFile(context, AUTHORITY, imageFile)

        return Pair(imageFile, imageUri)
    }

    /**
     * Decodifica una imagen desde Uri con submuestreo para evitar OutOfMemoryError.
     *
     * @param context Contexto de la aplicación.
     * @param uri Uri de la imagen.
     * @param reqWidth Ancho deseado en píxeles.
     * @param reqHeight Alto deseado en píxeles.
     * @return Bitmap submuestreado o null si hay error.
     */
    fun decodeSampledBitmap(
        context: Context,
        uri: Uri,
        reqWidth: Int = 512,
        reqHeight: Int = 512
    ): Bitmap? {
        return try {
            // Paso 1: Leer dimensiones sin cargar el bitmap completo
            val options = BitmapFactory.Options().apply {
                inJustDecodeBounds = true
            }
            context.contentResolver.openInputStream(uri)?.use { inputStream ->
                BitmapFactory.decodeStream(inputStream, null, options)
            }

            // Paso 2: Calcular factor de submuestreo
            options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight)

            // Paso 3: Decodificar con submuestreo
            options.inJustDecodeBounds = false
            context.contentResolver.openInputStream(uri)?.use { inputStream ->
                BitmapFactory.decodeStream(inputStream, null, options)
            }
        } catch (e: Exception) {
            e.printStackTrace()
            null
        }
    }

    /**
     * Calcula el factor de submuestreo óptimo.
     */
    private fun calculateInSampleSize(
        options: BitmapFactory.Options,
        reqWidth: Int,
        reqHeight: Int
    ): Int {
        val (height, width) = options.outHeight to options.outWidth
        var inSampleSize = 1

        if (height > reqHeight || width > reqWidth) {
            val halfHeight = height / 2
            val halfWidth = width / 2

            while (halfHeight / inSampleSize >= reqHeight &&
                halfWidth / inSampleSize >= reqWidth
            ) {
                inSampleSize *= 2
            }
        }

        return inSampleSize
    }

    /**
     * Carga una imagen en un ImageView usando Glide con placeholder y manejo de errores.
     */
    fun loadImageWithGlide(
        context: Context,
        uri: Uri?,
        imageView: ImageView,
        placeholderRes: Int = R.drawable.ic_placeholder,
        errorRes: Int = R.drawable.ic_broken_image
    ) {
        Glide.with(context)
            .load(uri)
            .placeholder(placeholderRes)
            .error(errorRes)
            .centerCrop()
            .transition(DrawableTransitionOptions.withCrossFade())
            .into(imageView)
    }

    /**
     * Carga una imagen como miniatura (thumbnail) para el RecyclerView.
     */
    fun loadThumbnail(
        context: Context,
        uri: Uri?,
        imageView: ImageView
    ) {
        Glide.with(context)
            .load(uri)
            .placeholder(R.drawable.ic_placeholder)
            .error(R.drawable.ic_broken_image)
            .centerCrop()
            .override(120, 120) // Tamaño fijo para miniatura
            .transition(DrawableTransitionOptions.withCrossFade())
            .into(imageView)
    }
}
```

**Resultado Esperado:**

La clase `ImageHelper` proporciona métodos para crear archivos de imagen, decodificar con submuestreo eficiente y cargar imágenes con Glide.

**Verificación:**

- Compila el proyecto: **Build → Make Project**. No debe haber errores.
- Verifica que la importación de Glide (`com.bumptech.glide.Glide`) se resuelve correctamente.

---

### Paso 8 — Crear los Layouts XML

**Objetivo:** Diseñar los layouts para las 4 Activities y el item del RecyclerView.

**Instrucciones:**

1. **Layout principal** — Abre `res/layout/activity_main.xml` y reemplaza con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".ui.MainActivity">

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="@string/title_task_list"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/tvTaskCount"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textColor="@color/text_secondary"
        android:textSize="14sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvTitle"
        tools:text="4 tareas (2 pendientes)" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvTareas"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="12dp"
        android:layout_marginBottom="8dp"
        app:layout_constraintBottom_toTopOf="@id/btnAddTask"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvTaskCount"
        tools:listitem="@layout/item_tarea" />

    <TextView
        android:id="@+id/tvEmpty"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/empty_task_list"
        android:textColor="@color/text_secondary"
        android:textSize="16sp"
        android:visibility="gone"
        app:layout_constraintBottom_toTopOf="@id/btnAddTask"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvTaskCount" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnAddTask"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginEnd="4dp"
        android:text="@string/btn_add_task"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/btnMediaPlayer"
        app:layout_constraintStart_toStartOf="parent" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnMediaPlayer"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="4dp"
        android:text="@string/btn_media_player"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/btnAddTask" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

2. **Item del RecyclerView** — Crea `res/layout/item_tarea.xml`:
   - Clic derecho sobre `res/layout` → **New → Layout Resource File**.
   - **File name:** `item_tarea` → **OK**.

   Reemplaza su contenido con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginBottom="8dp"
    app:cardCornerRadius="8dp"
    app:cardElevation="2dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="12dp">

        <ImageView
            android:id="@+id/ivThumbnail"
            android:layout_width="56dp"
            android:layout_height="56dp"
            android:contentDescription="Imagen de la tarea"
            android:scaleType="centerCrop"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:src="@drawable/ic_placeholder" />

        <TextView
            android:id="@+id/tvTareaTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="12dp"
            android:layout_marginEnd="8dp"
            android:textSize="16sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toStartOf="@id/tvEstado"
            app:layout_constraintStart_toEndOf="@id/ivThumbnail"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="Título de la tarea" />

        <TextView
            android:id="@+id/tvTareaDesc"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="12dp"
            android:layout_marginTop="4dp"
            android:layout_marginEnd="8dp"
            android:textColor="@color/text_secondary"
            android:textSize="13sp"
            app:layout_constraintEnd_toStartOf="@id/tvEstado"
            app:layout_constraintStart_toEndOf="@id/ivThumbnail"
            app:layout_constraintTop_toBottomOf="@id/tvTareaTitle"
            tools:text="Descripción corta de la tarea..." />

        <TextView
            android:id="@+id/tvEstado"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="12sp"
            android:textStyle="italic"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="Pendiente" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</com.google.android.material.card.MaterialCardView>
```

3. **Layout para agregar tarea** — Crea `res/layout/activity_add_task.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.AddTaskActivity">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <TextView
            android:id="@+id/tvAddTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:text="@string/title_add_task"
            android:textSize="24sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilTitle"
            style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvAddTitle">

            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etTaskTitle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:hint="@string/hint_task_title"
                android:inputType="textCapSentences"
                android:maxLines="1" />

        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilDescription"
            style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tilTitle">

            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etTaskDescription"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:hint="@string/hint_task_description"
                android:inputType="textMultiLine|textCapSentences"
                android:maxLines="4"
                android:minLines="2" />

        </com.google.android.material.textfield.TextInputLayout>

        <TextView
            android:id="@+id/tvImageLabel"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:text="@string/label_attached_image"
            android:textSize="14sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tilDescription" />

        <ImageView
            android:id="@+id/ivPreview"
            android:layout_width="0dp"
            android:layout_height="200dp"
            android:layout_marginTop="8dp"
            android:background="#F5F5F5"
            android:contentDescription="Vista previa de imagen"
            android:scaleType="centerCrop"
            android:src="@drawable/ic_placeholder"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvImageLabel" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnTakePhoto"
            style="@style/Widget.MaterialComponents.Button.OutlinedButton"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:layout_marginEnd="4dp"
            android:text="@string/btn_take_photo"
            app:icon="@drawable/ic_camera"
            app:layout_constraintEnd_toStartOf="@id/btnPickGallery"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/ivPreview" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnPickGallery"
            style="@style/Widget.MaterialComponents.Button.OutlinedButton"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="4dp"
            android:layout_marginTop="12dp"
            android:text="@string/btn_pick_gallery"
            app:icon="@drawable/ic_placeholder"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toEndOf="@id/btnTakePhoto"
            app:layout_constraintTop_toBottomOf="@id/ivPreview" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnSaveTask"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:text="@string/btn_save_task"
            android:textSize="16sp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/btnTakePhoto" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</ScrollView>
```

4. **Layout para detalle de tarea** — Crea `res/layout/activity_task_detail.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.TaskDetailActivity">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <TextView
            android:id="@+id/tvDetailTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textSize="22sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="Título de la tarea" />

        <TextView
            android:id="@+id/tvDetailEstado"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:textSize="14sp"
            android:textStyle="italic"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvDetailTitle"
            tools:text="Pendiente" />

        <ImageView
            android:id="@+id/ivDetailImage"
            android:layout_width="0dp"
            android:layout_height="250dp"
            android:layout_marginTop="16dp"
            android:background="#F5F5F5"
            android:contentDescription="Imagen de la tarea"
            android:scaleType="centerCrop"
            android:visibility="gone"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvDetailEstado"
            tools:src="@drawable/ic_placeholder"
            tools:visibility="visible" />

        <TextView
            android:id="@+id/tvDetailDescription"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:textSize="16sp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/ivDetailImage"
            tools:text="Descripción completa de la tarea..." />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMarkComplete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:layout_marginEnd="4dp"
            android:text="@string/btn_mark_complete"
            app:layout_constraintEnd_toStartOf="@id/btnDelete"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvDetailDescription" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDelete"
            style="@style/Widget.MaterialComponents.Button.OutlinedButton"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="4dp"
            android:layout_marginTop="24dp"
            android:text="@string/btn_delete_task"
            android:textColor="#F44336"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toEndOf="@id/btnMarkComplete"
            app:layout_constraintTop_toBottomOf="@id/tvDetailDescription"
            app:strokeColor="#F44336" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</ScrollView>
```

5. **Layout para reproductor multimedia** — Crea `res/layout/activity_media_player.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.MediaPlayerActivity">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <TextView
            android:id="@+id/tvMediaTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:text="@string/title_media_player"
            android:textSize="24sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <!-- Sección de Audio -->
        <TextView
            android:id="@+id/tvAudioLabel"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:text="@string/label_audio"
            android:textSize="18sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvMediaTitle" />

        <SeekBar
            android:id="@+id/seekBarAudio"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvAudioLabel" />

        <TextView
            android:id="@+id/tvAudioProgress"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:gravity="center"
            android:textSize="12sp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/seekBarAudio"
            tools:text="00:00 / 00:30" />

        <LinearLayout
            android:id="@+id/llAudioControls"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:gravity="center"
            android:orientation="horizontal"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvAudioProgress">

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnAudioPlay"
                style="@style/Widget.MaterialComponents.Button.OutlinedButton"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginEnd="8dp"
                android:text="@string/btn_play" />

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnAudioPause"
                style="@style/Widget.MaterialComponents.Button.OutlinedButton"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginEnd="8dp"
                android:text="@string/btn_pause" />

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnAudioStop"
                style="@style/Widget.MaterialComponents.Button.OutlinedButton"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/btn_stop" />

        </LinearLayout>

        <!-- Sección de Video -->
        <TextView
            android:id="@+id/tvVideoLabel"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="32dp"
            android:text="@string/label_video"
            android:textSize="18sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/llAudioControls" />

        <VideoView
            android:id="@+id/videoView"
            android:layout_width="0dp"
            android:layout_height="220dp"
            android:layout_marginTop="12dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvVideoLabel" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</ScrollView>
```

**Resultado Esperado:**

Los 5 archivos de layout están creados sin errores de XML. El preview del editor visual muestra los diseños correctamente.

**Verificación:**

- Abre cada layout en el editor visual (pestaña **Design**) y verifica que los componentes se muestran correctamente.
- Compila el proyecto: **Build → Make Project**. No debe haber errores de layout.

---

### Paso 9 — Crear el Adapter del RecyclerView

**Objetivo:** Implementar el adapter que muestra las tareas con miniatura de imagen, título, descripción corta y estado.

**Instrucciones:**

1. Clic derecho sobre el paquete `adapter` → **New → Kotlin Class/File** → **Class** → nombre: `TareaAdapter`.

2. Reemplaza el contenido de `TareaAdapter.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.adapter

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.cursokotlin.android.taskmanagermedia.databinding.ItemTareaBinding
import com.cursokotlin.android.taskmanagermedia.model.Tarea
import com.cursokotlin.android.taskmanagermedia.util.ImageHelper

class TareaAdapter(
    private var tareas: List<Tarea> = emptyList(),
    private val onItemClick: (Tarea) -> Unit
) : RecyclerView.Adapter<TareaAdapter.TareaViewHolder>() {

    inner class TareaViewHolder(
        private val binding: ItemTareaBinding
    ) : RecyclerView.ViewHolder(binding.root) {

        fun bind(tarea: Tarea) {
            binding.tvTareaTitle.text = tarea.titulo
            binding.tvTareaDesc.text = tarea.descripcionCorta()
            binding.tvEstado.text = tarea.estadoTexto()

            // Aplicar estilo visual según estado
            if (tarea.completada) {
                binding.tvTareaTitle.alpha = 0.6f
                binding.tvTareaDesc.alpha = 0.6f
                binding.root.alpha = 0.8f
            } else {
                binding.tvTareaTitle.alpha = 1.0f
                binding.tvTareaDesc.alpha = 1.0f
                binding.root.alpha = 1.0f
            }

            // Cargar miniatura de imagen
            if (tarea.tieneImagen()) {
                ImageHelper.loadThumbnail(
                    binding.root.context,
                    tarea.imagenUri,
                    binding.ivThumbnail
                )
            } else {
                binding.ivThumbnail.setImageResource(
                    com.cursokotlin.android.taskmanagermedia.R.drawable.ic_placeholder
                )
            }

            // Click listener
            binding.root.setOnClickListener {
                onItemClick(tarea)
            }
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TareaViewHolder {
        val binding = ItemTareaBinding.inflate(
            LayoutInflater.from(parent.context),
            parent,
            false
        )
        return TareaViewHolder(binding)
    }

    override fun onBindViewHolder(holder: TareaViewHolder, position: Int) {
        holder.bind(tareas[position])
    }

    override fun getItemCount(): Int = tareas.size

    fun actualizarTareas(nuevasTareas: List<Tarea>) {
        tareas = nuevasTareas
        notifyDataSetChanged()
    }
}
```

**Resultado Esperado:**

El adapter utiliza View Binding, muestra miniaturas con Glide, aplica estilos visuales diferenciados para tareas completadas y maneja clicks.

**Verificación:**

- Compila el proyecto: **Build → Make Project**. No debe haber errores.

---

### Paso 10 — Implementar MainActivity

**Objetivo:** Crear la Activity principal con RecyclerView, navegación a las demás Activities y detección de agitación del dispositivo.

**Instrucciones:**

1. Si la plantilla creó `MainActivity.kt` en el paquete raíz, muévela al paquete `ui`:
   - Clic derecho sobre `MainActivity.kt` → **Refactor → Move** → selecciona `com.cursokotlin.android.taskmanagermedia.ui` → **OK**.

   Si no existe, créala: clic derecho sobre el paquete `ui` → **New → Kotlin Class/File** → **Class** → nombre: `MainActivity`.

2. Reemplaza el contenido de `MainActivity.kt` con:

```kotlin
package com.cursokotlin.android.taskmanagermedia.ui

import android.content.Context
import android.content.Intent
import android.hardware.Sensor
import android.hardware.SensorEvent
import android.hardware.SensorEventListener
import android.hardware.SensorManager
import android.os.Bundle
import android.view.View
import android.widget.Toast
import androidx.activity.viewModels
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import com.cursokotlin.android.taskmanagermedia.R
import com.cursokotlin.android.taskmanagermedia.adapter.TareaAdapter
import com.cursokotlin.android.taskmanagermedia.databinding.ActivityMainBinding
import com.cursokotlin.android.taskmanagermedia.viewmodel.TareaViewModel
import kotlin.math.sqrt

class MainActivity : AppCompatActivity(), SensorEventListener {

    private lateinit var binding: ActivityMainBinding
    private val viewModel: TareaViewModel by viewModels()
    private lateinit var adapter: TareaAdapter

    // Sensor de acelerómetro
    private lateinit var sensorManager: SensorManager
    private var accelerometer: Sensor? = null
    private var lastShakeTime: Long = 0
    private val SHAKE_THRESHOLD = 12.0f
    private val SHAKE_COOLDOWN_MS = 2000L

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        setupRecyclerView()
        setupObservers()
        setupButtons()
        setupSensor()
    }

    private fun setupRecyclerView() {
        adapter = TareaAdapter { tarea ->
            val intent = Intent(this, TaskDetailActivity::class.java).apply {
                putExtra("TAREA_ID", tarea.id)
            }
            startActivity(intent)
        }
