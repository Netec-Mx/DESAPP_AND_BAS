---LAB_START---
LAB_ID: 07-00-01
---MARKDOWN---
# Laboratorio 7 — Desarrollo e integración del proyecto final Android

## 1. Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 216 minutos (6 fases) |
| **Complejidad** | Alta |
| **Nivel Bloom** | Crear |

---

## 2. Descripción general

Este laboratorio es el **proyecto integrador final** del curso básico de desarrollo Android. A lo largo de seis fases secuenciales, planearás, construirás, probarás y presentarás una aplicación Android completa llamada **NotaKMP** — una app de notas multimedia con persistencia local. El proyecto sintetiza todos los conocimientos adquiridos en los Laboratorios 1 al 6: configuración del entorno, Kotlin, Activities, navegación con Intents, construcción de UI con XML Layouts, integración multimedia y persistencia de datos. Al finalizar, entregarás un repositorio Git con código fuente funcional, APK de depuración y documentación.

---

## 3. Objetivos de aprendizaje

Al completar este laboratorio serás capaz de:

- [ ] Planear y documentar una aplicación Android original de nivel básico-intermedio con alcance acotado, pantallas identificadas, flujo de navegación y requisitos funcionales claros.
- [ ] Construir todas las pantallas de la aplicación con navegación funcional entre ellas, aplicando Activities, Intents explícitos y XML Layouts.
- [ ] Integrar al menos un elemento multimedia (imágenes desde galería, reproducción de audio o captura de cámara) gestionando correctamente el ciclo de vida.
- [ ] Implementar al menos un mecanismo de persistencia local (SharedPreferences, DataStore o Room) para que la app conserve datos entre sesiones.
- [ ] Ejecutar pruebas funcionales básicas sobre los flujos principales de la aplicación en AVD API 36 y AVD API 30, documentando y corrigiendo defectos críticos.

---

## 4. Prerrequisitos

### Conocimientos requeridos

| Conocimiento | Laboratorio de referencia |
|---|---|
| Configuración de Android Studio Quail 3, SDK y AVDs | Lab 1 |
| Fundamentos de Kotlin (clases, funciones, colecciones) | Lab 2 |
| Activities, ciclo de vida e Intents explícitos | Lab 3 |
| Construcción de UI con XML Layouts (ConstraintLayout, RecyclerView) | Lab 4 |
| Integración multimedia (MediaPlayer, galería, cámara) | Lab 5 |
| Persistencia local (SharedPreferences, DataStore o Room) | Lab 6 |
| Uso básico de Git para control de versiones | Todos |

### Acceso requerido

- Laboratorios 1 al 6 completados satisfactoriamente.
- Android Studio Quail 3 (2026.1.3 Patch 1) instalado y funcional.
- AVDs configurados para API 30, 36 y 37.
- Git 2.47.1 instalado y configurado con nombre y correo.
- Conexión a Internet para descarga de dependencias Gradle.

---

## 5. Entorno de laboratorio

### Hardware mínimo

| Componente | Requisito |
|---|---|
| Procesador | 64 bits, Intel i5 8ª gen. o AMD Ryzen 5, con VT-x/AMD-V habilitado |
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
| JDK | 11 (JavaVersion.VERSION_11) |
| compileSdk / targetSdk | 37 |
| minSdk | 30 |
| Compose BOM | 2026.02.01 (solo si se usa Compose) |
| Git | 2.47.1 |
| AVDs | API 30, 36 |

### Directorio de trabajo

Todo el proyecto se creará dentro del directorio estándar del curso:

**Windows:**
```
C:\AndroidCursoBasico\Lab7\NotaKMP\
```

**macOS / Linux:**
```
~/AndroidCursoBasico/Lab7/NotaKMP/
```

### Preparación inicial del directorio

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force -Path "C:\AndroidCursoBasico\Lab7"
```

**macOS / Linux (Terminal):**
```bash
mkdir -p ~/AndroidCursoBasico/Lab7
```

---

## 6. Pasos del laboratorio

---

### FASE 1 — Definición y planeación del proyecto (30 minutos)

#### Objetivo

Definir el alcance completo de la aplicación **NotaKMP** mediante un documento de planeación estructurado que servirá como guía para las fases restantes.

#### Instrucciones

1. **Crear el directorio del proyecto y el repositorio Git:**

   **Windows:**
   ```powershell
   cd C:\AndroidCursoBasico\Lab7
   mkdir NotaKMP
   cd NotaKMP
   git init
   ```

   **macOS / Linux:**
   ```bash
   cd ~/AndroidCursoBasico/Lab7
   mkdir NotaKMP
   cd NotaKMP
   git init
   ```

2. **Crear el archivo `PLANNING.md`** en la raíz del directorio `NotaKMP/` con el siguiente contenido. Este documento define el alcance completo del proyecto:

   ```markdown
   # PLANNING.md — NotaKMP

   ## Nombre del proyecto
   NotaKMP — Notas Multimedia con Persistencia

   ## Descripción
   Aplicación Android en Kotlin que permite crear, visualizar y eliminar
   notas de texto con imagen opcional, almacenadas de forma local
   mediante SharedPreferences (metadatos) y almacenamiento interno (imágenes).

   ## Usuario objetivo
   Estudiante o profesional que necesita tomar apuntes rápidos con soporte
   visual, sin conexión a internet.

   ## Funcionalidades MVP (Producto Mínimo Viable)
   1. Crear nota con título y cuerpo de texto.
   2. Ver lista de notas existentes en pantalla principal.
   3. Ver detalle completo de una nota seleccionada.
   4. Eliminar una nota desde la vista de detalle.
   5. Adjuntar imagen desde la galería del dispositivo.
   6. Persistir notas entre sesiones de la app.

   ## Funcionalidades opcionales (si el tiempo lo permite)
   - Reproducir un sonido de confirmación al guardar una nota.
   - Buscar notas por texto del título.

   ## Pantallas
   | Pantalla            | Descripción                                    | Activity                |
   |---------------------|------------------------------------------------|-------------------------|
   | Lista de notas      | RecyclerView con todas las notas creadas       | MainActivity            |
   | Detalle de nota     | Muestra título, texto e imagen de una nota     | NoteDetailActivity      |
   | Editor de nota      | Formulario para crear una nota nueva           | NoteEditorActivity      |

   ## Diagrama de navegación
   ```
   MainActivity ──(toca nota)──> NoteDetailActivity
   MainActivity ──(FAB +)──> NoteEditorActivity
   NoteEditorActivity ──(guardar)──> MainActivity
   NoteDetailActivity ──(eliminar)──> MainActivity
   ```

   ## Tecnologías y componentes del curso
   | Funcionalidad             | Componente Android                    | Lab ref. |
   |---------------------------|---------------------------------------|----------|
   | Lista de notas            | RecyclerView + ConstraintLayout       | Lab 4    |
   | Crear/ver nota            | Activity + EditText + TextView        | Lab 3-4  |
   | Navegación                | Intent explícito                      | Lab 3    |
   | Imagen desde galería      | ActivityResultLauncher + GetContent   | Lab 5    |
   | Persistencia              | SharedPreferences + JSON (Gson)       | Lab 6    |
   | Ciclo de vida             | onSaveInstanceState / onDestroy       | Lab 3    |

   ## Estructura de datos
   Cada nota se representa como un objeto con:
   - `id`: String (UUID generado)
   - `title`: String
   - `body`: String
   - `imagePath`: String? (ruta interna, nullable)
   - `createdAt`: Long (timestamp)

   Las notas se serializan a JSON y se almacenan en SharedPreferences
   bajo la clave "notes_list".

   ## Criterios de aceptación
   1. La app muestra una lista de notas al abrirse.
   2. Se puede crear una nota con título y texto obligatorios.
   3. Se puede adjuntar una imagen desde la galería.
   4. Las notas persisten al cerrar y reabrir la app.
   5. Se puede eliminar una nota desde el detalle.
   6. La app no produce crashes en AVD API 36 ni AVD API 30.
   7. La rotación de pantalla no pierde datos del formulario.

   ## Plan de trabajo (fases del laboratorio)
   - Fase 2: Crear proyecto, pantallas y navegación
   - Fase 3: Integrar selección de imagen desde galería
   - Fase 4: Implementar persistencia con SharedPreferences
   - Fase 5: Pruebas funcionales en AVD API 36 y API 30
   - Fase 6: Presentación final
   ```

3. **Dibujar el diagrama de flujo de navegación** en papel o herramienta digital. El diagrama debe mostrar:
   - `MainActivity` (lista) conecta con `NoteDetailActivity` al tocar una nota.
   - `MainActivity` conecta con `NoteEditorActivity` al presionar el botón flotante (+).
   - `NoteEditorActivity` regresa a `MainActivity` al guardar.
   - `NoteDetailActivity` regresa a `MainActivity` al eliminar.

4. **Realizar el commit inicial:**

   ```bash
   git add PLANNING.md
   git commit -m "Fase 1: Documento de planeación del proyecto NotaKMP"
   ```

#### Resultado esperado

- Archivo `PLANNING.md` completo en la raíz del directorio del proyecto.
- Repositorio Git inicializado con un commit.
- Diagrama de navegación documentado (dentro del archivo o como boceto adjunto).

#### Verificación

```bash
git log --oneline
```

Debe mostrar un commit con el mensaje de la Fase 1. Verifica que el archivo `PLANNING.md` contiene las 10 secciones listadas.

---

### FASE 2 — Construcción de pantallas, navegación y recursos (60 minutos)

#### Objetivo

Crear el proyecto Android desde cero, implementar las tres Activities con sus layouts XML y configurar la navegación completa entre pantallas usando Intents explícitos.

#### Instrucciones

##### Paso 2.1 — Crear el proyecto en Android Studio

1. Abrir **Android Studio Quail 3 (2026.1.3 Patch 1)**.
2. Seleccionar **File > New > New Project**.
3. Elegir la plantilla **Empty Views Activity** (NO "Empty Activity").
4. Configurar:
   - **Name:** `NotaKMP`
   - **Package name:** `com.cursokotlin.android.notakmp`
   - **Save location:** `C:\AndroidCursoBasico\Lab7\NotaKMP` (Windows) o `~/AndroidCursoBasico/Lab7/NotaKMP` (macOS/Linux)
   - **Language:** Kotlin
   - **Minimum SDK:** API 30 (Android 11)
   - **Build configuration language:** Kotlin DSL (build.gradle.kts)
5. Hacer clic en **Finish** y esperar la sincronización de Gradle.

##### Paso 2.2 — Configurar el catálogo de versiones

6. Abrir el archivo `gradle/libs.versions.toml` y reemplazar su contenido completo con:

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
gson = "2.11.0"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
androidx-recyclerview = { group = "androidx.recyclerview", name = "recyclerview", version.ref = "recyclerview" }
gson = { group = "com.google.code.gson", name = "gson", version.ref = "gson" }
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitExt" }
androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

##### Paso 2.3 — Configurar build.gradle.kts del proyecto raíz

7. Abrir `build.gradle.kts` (nivel proyecto) y verificar que contiene:

```kotlin
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
}
```

##### Paso 2.4 — Configurar build.gradle.kts del módulo app

8. Abrir `app/build.gradle.kts` y reemplazar con:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "com.cursokotlin.android.notakmp"
    compileSdk = 37

    defaultConfig {
        applicationId = "com.cursokotlin.android.notakmp"
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
    implementation(libs.androidx.lifecycle.runtime.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.material)
    implementation(libs.androidx.constraintlayout)
    implementation(libs.androidx.recyclerview)
    implementation(libs.gson)

    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
}
```

9. Hacer clic en **Sync Now** en la barra amarilla superior. Esperar a que la sincronización termine sin errores.

##### Paso 2.5 — Crear el modelo de datos

10. Crear el paquete `model` dentro de `com.cursokotlin.android.notakmp`. Hacer clic derecho sobre el paquete base > **New > Package** > escribir `model`.

11. Crear el archivo `Note.kt` dentro del paquete `model`:

```kotlin
package com.cursokotlin.android.notakmp.model

import java.util.UUID

data class Note(
    val id: String = UUID.randomUUID().toString(),
    val title: String,
    val body: String,
    val imagePath: String? = null,
    val createdAt: Long = System.currentTimeMillis()
)
```

##### Paso 2.6 — Crear el layout de la pantalla principal (lista de notas)

12. Abrir `app/src/main/res/layout/activity_main.xml` y reemplazar con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvEmptyState"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/empty_notes_message"
        android:textSize="16sp"
        android:textColor="@color/gray_text"
        android:visibility="gone"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvNotes"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:padding="8dp"
        android:clipToPadding="false"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:listitem="@layout/item_note" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fabAddNote"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:contentDescription="@string/add_note"
        android:src="@android:drawable/ic_input_add"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

13. Crear el layout del ítem de la lista. Hacer clic derecho en `res/layout` > **New > Layout Resource File** > nombre: `item_note.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="4dp"
    app:cardCornerRadius="8dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="12dp">

        <TextView
            android:id="@+id/tvNoteTitle"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textSize="18sp"
            android:textStyle="bold"
            android:maxLines="1"
            android:ellipsize="end" />

        <TextView
            android:id="@+id/tvNotePreview"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:textSize="14sp"
            android:maxLines="2"
            android:ellipsize="end"
            android:textColor="@color/gray_text" />

    </LinearLayout>

</com.google.android.material.card.MaterialCardView>
```

##### Paso 2.7 — Crear el layout del editor de notas

14. Crear `activity_note_editor.xml` en `res/layout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:hint="@string/hint_title"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent">

            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etTitle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:inputType="textCapSentences"
                android:maxLines="1" />

        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilBody"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:hint="@string/hint_body"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tilTitle">

            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etBody"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:inputType="textMultiLine|textCapSentences"
                android:minLines="4"
                android:gravity="top" />

        </com.google.android.material.textfield.TextInputLayout>

        <ImageView
            android:id="@+id/ivSelectedImage"
            android:layout_width="0dp"
            android:layout_height="200dp"
            android:layout_marginTop="16dp"
            android:scaleType="centerCrop"
            android:visibility="gone"
            android:contentDescription="@string/selected_image"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tilBody" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnAttachImage"
            style="@style/Widget.Material3.Button.OutlinedButton"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="@string/attach_image"
            app:icon="@android:drawable/ic_menu_gallery"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/ivSelectedImage" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnSaveNote"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:text="@string/save_note"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/btnAttachImage" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</ScrollView>
```

##### Paso 2.8 — Crear el layout del detalle de nota

15. Crear `activity_note_detail.xml` en `res/layout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <TextView
            android:id="@+id/tvDetailTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textSize="24sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <TextView
            android:id="@+id/tvDetailDate"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:textSize="12sp"
            android:textColor="@color/gray_text"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvDetailTitle" />

        <ImageView
            android:id="@+id/ivDetailImage"
            android:layout_width="0dp"
            android:layout_height="250dp"
            android:layout_marginTop="16dp"
            android:scaleType="centerCrop"
            android:visibility="gone"
            android:contentDescription="@string/note_image"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvDetailDate" />

        <TextView
            android:id="@+id/tvDetailBody"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:textSize="16sp"
            android:lineSpacingMultiplier="1.3"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/ivDetailImage" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDeleteNote"
            style="@style/Widget.Material3.Button.OutlinedButton"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="32dp"
            android:text="@string/delete_note"
            android:textColor="@color/red_delete"
            app:strokeColor="@color/red_delete"
            app:icon="@android:drawable/ic_menu_delete"
            app:iconTint="@color/red_delete"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tvDetailBody" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</ScrollView>
```

##### Paso 2.9 — Crear los recursos de strings y colores

16. Abrir `res/values/strings.xml` y reemplazar con:

```xml
<resources>
    <string name="app_name">NotaKMP</string>
    <string name="empty_notes_message">No hay notas. ¡Crea la primera!</string>
    <string name="add_note">Agregar nota</string>
    <string name="hint_title">Título de la nota</string>
    <string name="hint_body">Escribe tu nota aquí…</string>
    <string name="attach_image">Adjuntar imagen</string>
    <string name="save_note">Guardar nota</string>
    <string name="delete_note">Eliminar nota</string>
    <string name="selected_image">Imagen seleccionada</string>
    <string name="note_image">Imagen de la nota</string>
    <string name="title_editor">Nueva nota</string>
    <string name="title_detail">Detalle</string>
    <string name="error_empty_title">El título no puede estar vacío</string>
    <string name="error_empty_body">El cuerpo no puede estar vacío</string>
    <string name="note_saved">Nota guardada</string>
    <string name="note_deleted">Nota eliminada</string>
    <string name="confirm_delete">¿Eliminar esta nota?</string>
    <string name="cancel">Cancelar</string>
    <string name="confirm">Confirmar</string>
</resources>
```

17. Abrir `res/values/colors.xml` y agregar los colores necesarios:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="gray_text">#FF757575</color>
    <color name="red_delete">#FFD32F2F</color>
</resources>
```

##### Paso 2.10 — Crear el adaptador del RecyclerView

18. Crear el paquete `adapter` dentro del paquete base. Crear `NoteAdapter.kt`:

```kotlin
package com.cursokotlin.android.notakmp.adapter

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.cursokotlin.android.notakmp.databinding.ItemNoteBinding
import com.cursokotlin.android.notakmp.model.Note

class NoteAdapter(
    private var notes: List<Note>,
    private val onNoteClick: (Note) -> Unit
) : RecyclerView.Adapter<NoteAdapter.NoteViewHolder>() {

    inner class NoteViewHolder(
        private val binding: ItemNoteBinding
    ) : RecyclerView.ViewHolder(binding.root) {

        fun bind(note: Note) {
            binding.tvNoteTitle.text = note.title
            binding.tvNotePreview.text = note.body
            binding.root.setOnClickListener { onNoteClick(note) }
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): NoteViewHolder {
        val binding = ItemNoteBinding.inflate(
            LayoutInflater.from(parent.context), parent, false
        )
        return NoteViewHolder(binding)
    }

    override fun onBindViewHolder(holder: NoteViewHolder, position: Int) {
        holder.bind(notes[position])
    }

    override fun getItemCount(): Int = notes.size

    fun updateNotes(newNotes: List<Note>) {
        notes = newNotes
        notifyDataSetChanged()
    }
}
```

##### Paso 2.11 — Implementar MainActivity (lista de notas)

19. Abrir `MainActivity.kt` y reemplazar con:

```kotlin
package com.cursokotlin.android.notakmp

import android.content.Intent
import android.os.Bundle
import android.view.View
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import com.cursokotlin.android.notakmp.adapter.NoteAdapter
import com.cursokotlin.android.notakmp.databinding.ActivityMainBinding
import com.cursokotlin.android.notakmp.model.Note
import com.cursokotlin.android.notakmp.storage.NoteStorage

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private lateinit var noteAdapter: NoteAdapter
    private lateinit var noteStorage: NoteStorage

    private val editorLauncher = registerForActivityResult(
        ActivityResultContracts.StartActivityForResult()
    ) { result ->
        if (result.resultCode == RESULT_OK) {
            loadNotes()
        }
    }

    private val detailLauncher = registerForActivityResult(
        ActivityResultContracts.StartActivityForResult()
    ) { result ->
        if (result.resultCode == RESULT_OK) {
            loadNotes()
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        noteStorage = NoteStorage(this)

        setupRecyclerView()
        setupFab()
        loadNotes()
    }

    private fun setupRecyclerView() {
        noteAdapter = NoteAdapter(emptyList()) { note ->
            val intent = Intent(this, NoteDetailActivity::class.java)
            intent.putExtra("NOTE_ID", note.id)
            detailLauncher.launch(intent)
        }
        binding.rvNotes.apply {
            layoutManager = LinearLayoutManager(this@MainActivity)
            adapter = noteAdapter
        }
    }

    private fun setupFab() {
        binding.fabAddNote.setOnClickListener {
            val intent = Intent(this, NoteEditorActivity::class.java)
            editorLauncher.launch(intent)
        }
    }

    private fun loadNotes() {
        val notes = noteStorage.getAllNotes()
        noteAdapter.updateNotes(notes)
        binding.tvEmptyState.visibility = if (notes.isEmpty()) View.VISIBLE else View.GONE
        binding.rvNotes.visibility = if (notes.isEmpty()) View.GONE else View.VISIBLE
    }
}
```

> **Nota:** La clase `NoteStorage` se creará en la Fase 4. Para que el proyecto compile durante la Fase 2, crea una versión provisional en el siguiente paso.

##### Paso 2.12 — Crear NoteStorage provisional

20. Crear el paquete `storage` y dentro de él el archivo `NoteStorage.kt` con una implementación temporal:

```kotlin
package com.cursokotlin.android.notakmp.storage

import android.content.Context
import com.cursokotlin.android.notakmp.model.Note

class NoteStorage(private val context: Context) {

    // Almacenamiento temporal en memoria (se reemplazará en Fase 4)
    companion object {
        private val notesInMemory = mutableListOf<Note>()
    }

    fun getAllNotes(): List<Note> {
        return notesInMemory.sortedByDescending { it.createdAt }
    }

    fun getNoteById(id: String): Note? {
        return notesInMemory.find { it.id == id }
    }

    fun saveNote(note: Note) {
        notesInMemory.add(note)
    }

    fun deleteNote(id: String) {
        notesInMemory.removeAll { it.id == id }
    }
}
```

##### Paso 2.13 — Implementar NoteEditorActivity

21. Crear la Activity `NoteEditorActivity.kt` en el paquete base:

```kotlin
package com.cursokotlin.android.notakmp

import android.net.Uri
import android.os.Bundle
import android.widget.Toast
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.notakmp.databinding.ActivityNoteEditorBinding
import com.cursokotlin.android.notakmp.model.Note
import com.cursokotlin.android.notakmp.storage.NoteStorage

class NoteEditorActivity : AppCompatActivity() {

    private lateinit var binding: ActivityNoteEditorBinding
    private lateinit var noteStorage: NoteStorage
    private var selectedImageUri: Uri? = null

    private val galleryLauncher = registerForActivityResult(
        ActivityResultContracts.GetContent()
    ) { uri: Uri? ->
        uri?.let {
            selectedImageUri = it
            binding.ivSelectedImage.setImageURI(it)
            binding.ivSelectedImage.visibility = android.view.View.VISIBLE
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityNoteEditorBinding.inflate(layoutInflater)
        setContentView(binding.root)

        title = getString(R.string.title_editor)
        noteStorage = NoteStorage(this)

        // Restaurar estado si hubo rotación
        savedInstanceState?.let {
            val uriString = it.getString("SELECTED_IMAGE_URI")
            if (uriString != null) {
                selectedImageUri = Uri.parse(uriString)
                binding.ivSelectedImage.setImageURI(selectedImageUri)
                binding.ivSelectedImage.visibility = android.view.View.VISIBLE
            }
        }

        binding.btnAttachImage.setOnClickListener {
            galleryLauncher.launch("image/*")
        }

        binding.btnSaveNote.setOnClickListener {
            saveNote()
        }
    }

    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        selectedImageUri?.let {
            outState.putString("SELECTED_IMAGE_URI", it.toString())
        }
    }

    private fun saveNote() {
        val titleText = binding.etTitle.text.toString().trim()
        val bodyText = binding.etBody.text.toString().trim()

        if (titleText.isEmpty()) {
            binding.tilTitle.error = getString(R.string.error_empty_title)
            return
        }
        binding.tilTitle.error = null

        if (bodyText.isEmpty()) {
            binding.tilBody.error = getString(R.string.error_empty_body)
            return
        }
        binding.tilBody.error = null

        val imagePath = selectedImageUri?.let { uri ->
            copyImageToInternalStorage(uri)
        }

        val note = Note(
            title = titleText,
            body = bodyText,
            imagePath = imagePath
        )

        noteStorage.saveNote(note)
        Toast.makeText(this, getString(R.string.note_saved), Toast.LENGTH_SHORT).show()
        setResult(RESULT_OK)
        finish()
    }

    private fun copyImageToInternalStorage(uri: Uri): String? {
        return try {
            val inputStream = contentResolver.openInputStream(uri) ?: return null
            val fileName = "note_img_${System.currentTimeMillis()}.jpg"
            val outputStream = openFileOutput(fileName, MODE_PRIVATE)
            inputStream.copyTo(outputStream)
            inputStream.close()
            outputStream.close()
            fileName
        } catch (e: Exception) {
            e.printStackTrace()
            null
        }
    }
}
```

##### Paso 2.14 — Implementar NoteDetailActivity

22. Crear `NoteDetailActivity.kt`:

```kotlin
package com.cursokotlin.android.notakmp

import android.graphics.BitmapFactory
import android.os.Bundle
import android.view.View
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.notakmp.databinding.ActivityNoteDetailBinding
import com.cursokotlin.android.notakmp.storage.NoteStorage
import com.google.android.material.dialog.MaterialAlertDialogBuilder
import java.text.SimpleDateFormat
import java.util.Date
import java.util.Locale

class NoteDetailActivity : AppCompatActivity() {

    private lateinit var binding: ActivityNoteDetailBinding
    private lateinit var noteStorage: NoteStorage
    private var noteId: String? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityNoteDetailBinding.inflate(layoutInflater)
        setContentView(binding.root)

        title = getString(R.string.title_detail)
        noteStorage = NoteStorage(this)

        noteId = intent.getStringExtra("NOTE_ID")
        val note = noteId?.let { noteStorage.getNoteById(it) }

        if (note == null) {
            Toast.makeText(this, "Nota no encontrada", Toast.LENGTH_SHORT).show()
            finish()
            return
        }

        binding.tvDetailTitle.text = note.title
        binding.tvDetailBody.text = note.body

        val dateFormat = SimpleDateFormat("dd/MM/yyyy HH:mm", Locale.getDefault())
        binding.tvDetailDate.text = dateFormat.format(Date(note.createdAt))

        note.imagePath?.let { fileName ->
            try {
                val inputStream = openFileInput(fileName)
                val bitmap = BitmapFactory.decodeStream(inputStream)
                inputStream.close()
                binding.ivDetailImage.setImageBitmap(bitmap)
                binding.ivDetailImage.visibility = View.VISIBLE
            } catch (e: Exception) {
                e.printStackTrace()
            }
        }

        binding.btnDeleteNote.setOnClickListener {
            showDeleteConfirmation()
        }
    }

    private fun showDeleteConfirmation() {
        MaterialAlertDialogBuilder(this)
            .setTitle(getString(R.string.delete_note))
            .setMessage(getString(R.string.confirm_delete))
            .setNegativeButton(getString(R.string.cancel), null)
            .setPositiveButton(getString(R.string.confirm)) { _, _ ->
                noteId?.let { id ->
                    noteStorage.deleteNote(id)
                    Toast.makeText(this, getString(R.string.note_deleted), Toast.LENGTH_SHORT).show()
                    setResult(RESULT_OK)
                    finish()
                }
            }
            .show()
    }
}
```

##### Paso 2.15 — Registrar las Activities en AndroidManifest.xml

23. Abrir `app/src/main/AndroidManifest.xml` y verificar/agregar las Activities nuevas dentro de la etiqueta `<application>`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.NotaKMP"
        tools:targetApi="31">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".NoteEditorActivity"
            android:exported="false"
            android:parentActivityName=".MainActivity" />

        <activity
            android:name=".NoteDetailActivity"
            android:exported="false"
            android:parentActivityName=".MainActivity" />

    </application>

</manifest>
```

##### Paso 2.16 — Compilar y probar la navegación

24. Hacer clic en **Build > Make Project** (Ctrl+F9 / Cmd+F9). Corregir cualquier error de compilación.

25. Ejecutar la app en el **AVD API 36**. Verificar:
    - La pantalla principal muestra el mensaje "No hay notas. ¡Crea la primera!".
    - El FAB (+) abre `NoteEditorActivity`.
    - Se puede escribir título y cuerpo.
    - Al guardar, se regresa a `MainActivity` y la nota aparece en la lista.
    - Al tocar una nota, se abre `NoteDetailActivity` con los datos correctos.
    - El botón eliminar muestra el diálogo de confirmación y elimina la nota.

26. Realizar commit:

```bash
git add -A
git commit -m "Fase 2: Pantallas, navegación y adaptador completados"
```

#### Resultado esperado

La aplicación compila sin errores, las tres pantallas están funcionales y la navegación entre ellas opera correctamente. Las notas se almacenan temporalmente en memoria (se perderán al cerrar la app — esto se resolverá en la Fase 4).

#### Verificación

- Crear 3 notas con distintos títulos → las 3 aparecen en la lista.
- Tocar una nota → se muestra el detalle correcto.
- Eliminar una nota → la lista se actualiza sin la nota eliminada.
- Presionar el botón "Atrás" del sistema desde el editor → regresa a la lista sin guardar.

---

### FASE 3 — Integración de multimedia (30 minutos)

#### Objetivo

Integrar la selección de imágenes desde la galería del dispositivo usando `ActivityResultLauncher` y almacenar la imagen en el almacenamiento interno de la app.

#### Instrucciones

> **Nota:** La funcionalidad de galería ya fue integrada en `NoteEditorActivity` durante la Fase 2 (el `galleryLauncher` y `copyImageToInternalStorage`). En esta fase se verifica su funcionamiento y se agrega opcionalmente la reproducción de un sonido de confirmación.

1. **Verificar la funcionalidad de galería:**
   - Ejecutar la app en el AVD API 36.
   - Crear una nueva nota.
   - Presionar "Adjuntar imagen" → debe abrirse el selector de archivos/galería del emulador.
   - Seleccionar una imagen → debe mostrarse en el `ImageView` del editor.
   - Guardar la nota → abrir el detalle → la imagen debe mostrarse correctamente.

2. **Agregar un sonido de confirmación al guardar (funcionalidad opcional).** Primero, obtener un archivo de audio corto en formato `.mp3` o `.ogg` (un sonido de "ding" o confirmación). Colocarlo en `app/src/main/res/raw/`. Si no se tiene uno disponible, se puede crear un archivo vacío para la estructura y saltar este paso.

   Crear el directorio `raw` si no existe: clic derecho en `res` > **New > Android Resource Directory** > Resource type: `raw`.

   Copiar el archivo de audio (ejemplo: `confirmation.mp3`) dentro de `res/raw/`.

3. **Modificar `NoteEditorActivity.kt`** para agregar la reproducción del sonido. Agregar el import y la propiedad del MediaPlayer:

```kotlin
import android.media.MediaPlayer
```

Agregar como propiedad de la clase:

```kotlin
private var mediaPlayer: MediaPlayer? = null
```

Modificar el método `saveNote()` para reproducir el sonido justo antes del `finish()`:

```kotlin
private fun saveNote() {
    val titleText = binding.etTitle.text.toString().trim()
    val bodyText = binding.etBody.text.toString().trim()

    if (titleText.isEmpty()) {
        binding.tilTitle.error = getString(R.string.error_empty_title)
        return
    }
    binding.tilTitle.error = null

    if (bodyText.isEmpty()) {
        binding.tilBody.error = getString(R.string.error_empty_body)
        return
    }
    binding.tilBody.error = null

    val imagePath = selectedImageUri?.let { uri ->
        copyImageToInternalStorage(uri)
    }

    val note = Note(
        title = titleText,
        body = bodyText,
        imagePath = imagePath
    )

    noteStorage.saveNote(note)

    // Reproducir sonido de confirmación
    try {
        mediaPlayer = MediaPlayer.create(this, R.raw.confirmation)
        mediaPlayer?.setOnCompletionListener { mp ->
            mp.release()
        }
        mediaPlayer?.start()
    } catch (e: Exception) {
        // Si no hay archivo de audio, continuar sin error
    }

    Toast.makeText(this, getString(R.string.note_saved), Toast.LENGTH_SHORT).show()
    setResult(RESULT_OK)
    finish()
}
```

4. **Gestionar el ciclo de vida del MediaPlayer.** Agregar `onDestroy` en `NoteEditorActivity`:

```kotlin
override fun onDestroy() {
    super.onDestroy()
    mediaPlayer?.release()
    mediaPlayer = null
}
```

5. **Realizar commit:**

```bash
git add -A
git commit -m "Fase 3: Integración multimedia - galería y sonido de confirmación"
```

#### Resultado esperado

- Las imágenes seleccionadas desde la galería se muestran tanto en el editor como en el detalle de la nota.
- Las imágenes se almacenan en el almacenamiento interno de la app (no se pierden si la URI original cambia).
- El sonido de confirmación se reproduce al guardar (si el archivo de audio está presente).
- No hay fugas de memoria del MediaPlayer.

#### Verificación

- Crear una nota con imagen → cerrar el detalle → volver a abrir el detalle → la imagen sigue visible.
- Rotar la pantalla en el editor con imagen seleccionada → la imagen se mantiene (gracias a `onSaveInstanceState`).
- Si se agregó el sonido: guardar una nota → se escucha el sonido de confirmación.

---

### FASE 4 — Persistencia simple con SharedPreferences (30 minutos)

#### Objetivo

Reemplazar el almacenamiento temporal en memoria por persistencia real usando SharedPreferences con serialización JSON (Gson), para que las notas sobrevivan al cierre de la aplicación.

#### Instrucciones

1. **Verificar que Gson está en las dependencias.** Abrir `app/build.gradle.kts` y confirmar que la línea `implementation(libs.gson)` está presente (se agregó en la Fase 2). La versión `2.11.0` ya está definida en `libs.versions.toml`.

2. **Reemplazar completamente el contenido de `NoteStorage.kt`** con la implementación persistente:

```kotlin
package com.cursokotlin.android.notakmp.storage

import android.content.Context
import android.content.SharedPreferences
import com.cursokotlin.android.notakmp.model.Note
import com.google.gson.Gson
import com.google.gson.reflect.TypeToken

class NoteStorage(context: Context) {

    companion object {
        private const val PREFS_NAME = "notakmp_prefs"
        private const val KEY_NOTES = "notes_list"
    }

    private val prefs: SharedPreferences =
        context.getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
    private val gson = Gson()

    fun getAllNotes(): List<Note> {
        val json = prefs.getString(KEY_NOTES, null) ?: return emptyList()
        val type = object : TypeToken<List<Note>>() {}.type
        return try {
            val notes: List<Note> = gson.fromJson(json, type)
            notes.sortedByDescending { it.createdAt }
        } catch (e: Exception) {
            e.printStackTrace()
            emptyList()
        }
    }

    fun getNoteById(id: String): Note? {
        return getAllNotes().find { it.id == id }
    }

    fun saveNote(note: Note) {
        val currentNotes = getAllNotes().toMutableList()
        currentNotes.add(note)
        saveAllNotes(currentNotes)
    }

    fun deleteNote(id: String) {
        val currentNotes = getAllNotes().toMutableList()
        currentNotes.removeAll { it.id == id }
        saveAllNotes(currentNotes)
    }

    private fun saveAllNotes(notes: List<Note>) {
        val json = gson.toJson(notes)
        prefs.edit().putString(KEY_NOTES, json).apply()
    }
}
```

3. **Eliminar el companion object temporal** que contenía `notesInMemory`. El nuevo `NoteStorage` ya no lo necesita.

4. **Compilar el proyecto** (Build > Make Project). No deben existir errores ya que la interfaz pública de `NoteStorage` no cambió.

5. **Probar la persistencia:**
   - Ejecutar la app en el AVD API 36.
   - Crear 2 notas (una con imagen, otra sin imagen).
   - Verificar que ambas aparecen en la lista.
   - **Cerrar la app completamente:** en el emulador, presionar el botón de apps recientes y deslizar la app para cerrarla.
   - Volver a abrir la app desde el launcher.
   - **Las 2 notas deben seguir presentes.**
   - Abrir el detalle de la nota con imagen → la imagen debe mostrarse correctamente.

6. **Probar la eliminación persistente:**
   - Eliminar una nota desde el detalle.
   - Cerrar y reabrir la app.
   - Solo debe quedar la nota no eliminada.

7. **Realizar commit:**

```bash
git add -A
git commit -m "Fase 4: Persistencia con SharedPreferences y Gson implementada"
```

#### Resultado esperado

Las notas se almacenan en SharedPreferences como JSON serializado. Al cerrar y reabrir la app, todas las notas persisten. Las imágenes almacenadas en el almacenamiento interno también persisten entre sesiones.

#### Verificación

```bash
# Verificar que SharedPreferences se creó en el emulador (desde terminal del sistema)
adb shell run-as com.cursokotlin.android.notakmp cat shared_prefs/notakmp_prefs.xml
```

La salida debe mostrar un XML con una entrada `notes_list` que contiene el JSON de las notas.

---

### FASE 5 — Pruebas funcionales básicas (30 minutos)

#### Objetivo

Ejecutar una batería de pruebas funcionales manuales siguiendo una checklist estructurada en dos AVDs (API 36 y API 30), documentando defectos encontrados y corrigiendo los críticos.

#### Instrucciones

1. **Crear el archivo `TESTING.md`** en la raíz del proyecto con la siguiente checklist:

```markdown
# TESTING.md — Pruebas funcionales de NotaKMP

## Dispositivos de prueba
- [ ] AVD API 36 (dispositivo principal)
- [ ] AVD API 30 (validación minSdk)

## Checklist de pruebas funcionales

### Navegación
- [ ] T01: La app abre sin crashes en MainActivity
- [ ] T02: FAB (+) abre NoteEditorActivity
- [ ] T03: Guardar nota regresa a MainActivity
- [ ] T04: Tocar nota abre NoteDetailActivity con datos correctos
- [ ] T05: Eliminar nota regresa a MainActivity sin la nota
- [ ] T06: Botón Atrás del sistema funciona correctamente en cada pantalla

### Persistencia
- [ ] T07: Notas persisten al cerrar y reabrir la app
- [ ] T08: Eliminar nota persiste al cerrar y reabrir la app
- [ ] T09: Imágenes adjuntas persisten al cerrar y reabrir la app

### Multimedia
- [ ] T10: Botón "Adjuntar imagen" abre el selector de galería
- [ ] T11: Imagen seleccionada se muestra en el editor
- [ ] T12: Imagen se muestra en el detalle de la nota

### Validación de entradas
- [ ] T13: Guardar con título vacío muestra error
- [ ] T14: Guardar con cuerpo vacío muestra error
- [ ] T15: Guardar con campos válidos funciona correctamente

### Rotación de pantalla
- [ ] T16: Rotar en editor no pierde texto escrito
- [ ] T17: Rotar en editor no pierde imagen seleccionada
- [ ] T18: Rotar en detalle no produce crash

### Estado vacío
- [ ] T19: Sin notas, se muestra mensaje "No hay notas"
- [ ] T20: Al crear primera nota, desaparece el mensaje vacío

## Defectos encontrados
| ID | Descripción | Severidad | Estado |
|----|-------------|-----------|--------|
|    |             |           |        |

## Notas de prueba
- Fecha:
- Tester:
```

2. **Ejecutar las pruebas en AVD API 36:**
   - Iniciar el AVD API 36 desde el Device Manager.
   - Ejecutar la app (Run > Run 'app').
   - Recorrer cada prueba de la checklist T01 a T20 de forma secuencial.
   - Marcar cada prueba como pasada o fallida en `TESTING.md`.
   - Documentar cualquier defecto encontrado en la tabla de defectos.

3. **Verificar la rotación de pantalla (T16-T18):**
   - En el editor, escribir un título y un cuerpo de texto.
   - Seleccionar una imagen.
   - Rotar el emulador (Ctrl+Flecha izquierda o Ctrl+Flecha derecha).
   - Verificar que el texto y la imagen se conservan.

   > **Problema esperado:** Los `EditText` dentro de `TextInputEditText` conservan su texto automáticamente si tienen un `android:id` definido. La imagen se conserva gracias al `onSaveInstanceState` implementado en la Fase 2. Si algún dato se pierde, agregar la restauración correspondiente.

4. **Ejecutar las pruebas en AVD API 30:**
   - Cambiar al AVD API 30 en el Device Manager.
   - Ejecutar la app.
   - Repetir las pruebas T01, T07, T10, T13 y T15 como mínimo (pruebas representativas).
   - Verificar que no hay crashes ni comportamientos diferentes.

5. **Corregir defectos críticos** (crashes, pérdida de datos, navegación rota). Los defectos cosméticos o menores se documentan pero no es obligatorio corregirlos.

6. **Realizar commit:**

```bash
git add -A
git commit -m "Fase 5: Pruebas funcionales completadas en AVD API 36 y API 30"
```

#### Resultado esperado

- `TESTING.md` completo con todas las pruebas marcadas.
- Todas las pruebas T01-T20 pasan en AVD API 36.
- Las pruebas representativas pasan en AVD API 30.
- Defectos críticos corregidos; defectos menores documentados.

#### Verificación

Revisar `TESTING.md` y confirmar que:
- Ninguna prueba tiene estado "fallida" sin corrección aplicada.
- La tabla de defectos documenta al menos la revisión (puede estar vacía si todo pasó).

---

### FASE 6 — Presentación final y entrega (36 minutos)

#### Objetivo

Generar el APK de depuración, preparar la documentación final del proyecto y realizar la presentación estructurada de la aplicación.

#### Instrucciones

1. **Generar el APK de depuración:**
   - En Android Studio: **Build > Build Bundle(s) / APK(s) > Build APK(s)**.
   - Esperar a que termine la compilación.
   - Hacer clic en **locate** en la notificación para encontrar el archivo.
   - El APK se genera en: `app/build/outputs/apk/debug/app-debug.apk`.
   - Copiar el APK a la raíz del proyecto:

   **Windows:**
   ```powershell
   copy app\build\outputs\apk\debug\app-debug.apk .\NotaKMP-debug.apk
   ```

   **macOS / Linux:**
   ```bash
   cp app/build/outputs/apk/debug/app-debug.apk ./NotaKMP-debug.apk
   ```

2. **Crear el archivo `README.md`** en la raíz del proyecto:

```markdown
# NotaKMP — Notas Multimedia con Persistencia

## Descripción
Aplicación Android en Kotlin que permite crear, visualizar y eliminar
notas de texto con imagen opcional, almacenadas localmente con
SharedPreferences.

## Requisitos
- Android 11 (API 30) o superior
- Android Studio Quail 3 (2026.1.3 Patch 1)
- Kotlin 2.2.10 / AGP 9.3.2

## Instalación desde código fuente
1. Clonar el repositorio.
2. Abrir el proyecto en Android Studio.
3. Sincronizar Gradle (File > Sync Project with Gradle Files).
4. Ejecutar en un AVD API 30+ o dispositivo físico.

## Instalación del APK
1. Transferir `NotaKMP-debug.apk` al dispositivo Android.
2. Habilitar "Instalar apps de fuentes desconocidas" si es necesario.
3. Abrir el APK e instalar.

## Estructura del proyecto
```
com.cursokotlin.android.notakmp/
├── model/
│   └── Note.kt                 # Modelo de datos
├── adapter/
│   └── NoteAdapter.kt          # Adaptador RecyclerView
├── storage/
│   └── NoteStorage.kt          # Persistencia SharedPreferences
├── MainActivity.kt              # Lista de notas
├── NoteEditorActivity.kt        # Crear nota nueva
└── NoteDetailActivity.kt        # Ver/eliminar nota
```

## Tecnologías utilizadas
- Kotlin 2.2.10
- Android Views + XML Layouts
- ConstraintLayout + RecyclerView
- SharedPreferences + Gson
- ActivityResultLauncher (galería)
- MediaPlayer (sonido de confirmación)
- Material Design 3 Components

## Autor
[Tu nombre] — Curso Básico de Desarrollo Android
```

3. **Crear el archivo `.gitignore`** si no existe, con el contenido apropiado para Android:

```
*.iml
.gradle
/local.properties
/.idea
/build
/app/build
/captures
.externalNativeBuild
.cxx
*.apk
*.ap_
*.aab
```

> **Nota:** Aunque `.gitignore` excluye APKs por defecto, para la entrega del curso se puede incluir `NotaKMP-debug.apk` explícitamente con `git add -f NotaKMP-debug.apk` si el instructor lo requiere.

4. **Realizar el commit final:**

```bash
git add -A
git commit -m "Fase 6: README, APK de depuración y documentación final"
```

5. **Verificar la estructura final del repositorio:**

```bash
git log --oneline
```

Debe mostrar al menos 5 commits (uno por fase):

```
xxxxxxx Fase 6: README, APK de depuración y documentación final
xxxxxxx Fase 5: Pruebas funcionales completadas en AVD API 36 y API 30
xxxxxxx Fase 4: Persistencia con SharedPreferences y Gson implementada
xxxxxxx Fase 3: Integración multimedia - galería y sonido de confirmación
xxxxxxx Fase 2: Pantallas, navegación y adaptador completados
xxxxxxx Fase 1: Documento de planeación del proyecto NotaKMP
```

6. **Preparar la presentación (5-7 minutos por estudiante/equipo).** La presentación debe cubrir:

   - **Demostración en vivo** (2-3 min): Ejecutar la app en el emulador mostrando el flujo completo: crear nota → adjuntar imagen → guardar → ver en lista → ver detalle → eliminar → cerrar y reabrir para demostrar persistencia.
   - **Arquitectura y decisiones técnicas** (1-2 min): Explicar la estructura de paquetes, por qué se eligió SharedPreferences+Gson, cómo se maneja el almacenamiento de imágenes.
   - **Retos y soluciones** (1 min): Describir al menos un problema encontrado durante el desarrollo y cómo se resolvió.
   - **Reflexión** (1 min): Qué se mejoraría con más tiempo (ej: migrar a Room, agregar búsqueda, mejorar UI).

7. **Realizar la presentación** ante el instructor y compañeros. Recibir retroalimentación constructiva.

#### Resultado esperado

- APK de depuración generado (`NotaKMP-debug.apk`).
- `README.md` con instrucciones de instalación y uso.
- Repositorio Git con historial limpio de 5-6 commits.
- Presentación completada con demostración funcional.

#### Verificación

Verificar la estructura final del proyecto:

```
NotaKMP/
├── .git/
├── .gitignore
├── PLANNING.md
├── TESTING.md
├── README.md
├── NotaKMP-debug.apk
├── gradle/
│   └── libs.versions.toml
├── build.gradle.kts
├── settings.gradle.kts
└── app/
    ├── build.gradle.kts
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/cursokotlin/android/notakmp/
        │   ├── MainActivity.kt
        │   ├── NoteEditorActivity.kt
        │   ├── NoteDetailActivity.kt
        │   ├── model/Note.kt
        │   ├── adapter/NoteAdapter.kt
        │   └── storage/NoteStorage.kt
        └── res/
            ├── layout/
            │   ├── activity_main.xml
            │   ├── activity_note_editor.xml
            │   ├── activity_note_detail.xml
            │   └── item_note.xml
            ├── values/
            │   ├── strings.xml
            │   └── colors.xml
            └── raw/
                └── confirmation.mp3 (opcional)
```

---

## 7. Validación y pruebas finales

Ejecutar la siguiente secuencia de validación completa como verificación final del proyecto:

| # | Prueba | Comando / Acción | Resultado esperado |
|---|--------|-------------------|--------------------|
| 1 | Compilación limpia | **Build > Clean Project**, luego **Build > Rebuild Project** | BUILD SUCCESSFUL sin errores ni warnings críticos |
| 2 | Ejecución en AVD API 36 | **Run > Run 'app'** en AVD API 36 | La app abre en MainActivity sin crashes |
| 3 | Flujo completo | Crear nota con imagen → ver lista → ver detalle → eliminar | Todas las transiciones funcionan correctamente |
| 4 | Persistencia | Cerrar app completamente → reabrir | Las notas guardadas siguen presentes |
| 5 | Compatibilidad minSdk | Ejecutar en AVD API 30 | La app funciona sin crashes ni diferencias funcionales |
| 6 | Rotación | Rotar pantalla en editor con datos ingresados | Los datos no se pierden |
| 7 | Validación de entrada | Intentar guardar nota con campos vacíos | Se muestran mensajes de error apropiados |
| 8 | Historial Git | `git log --oneline` | Mínimo 5 commits con mensajes descriptivos |
| 9 | APK generado | Verificar existencia de `NotaKMP-debug.apk` | Archivo presente en la raíz del proyecto |

---

## 8. Solución de problemas

### Problema 1: La imagen seleccionada desde la galería no se muestra en el detalle de la nota

**Síntomas:** Al crear una nota con imagen adjunta, la imagen se ve correctamente en el editor (`NoteEditorActivity`), pero al abrir el detalle (`NoteDetailActivity`) la imagen no aparece. No hay crash, simplemente el `ImageView` permanece invisible.

**Causa:** El método `copyImageToInternalStorage()` puede estar fallando silenciosamente, devolviendo `null`. Esto ocurre si el `ContentResolver` no puede abrir el `InputStream` de la URI proporcionada por el selector de galería, típicamente porque la URI temporal ya expiró o porque el emulador no tiene imágenes disponibles en la galería. Otra causa común es que el nombre del archivo guardado no coincide con el que se intenta leer en el detalle.

**Solución:**
1. Agregar logs de depuración en `copyImageToInternalStorage()`:
   ```kotlin
   private fun copyImageToInternalStorage(uri: Uri): String? {
       return try {
           val inputStream = contentResolver.openInputStream(uri)
           if (inputStream == null) {
               android.util.Log.e("NoteEditor", "No se pudo abrir InputStream para: $uri")
               return null
           }
           val fileName = "note_img_${System.currentTimeMillis()}.jpg"
           val outputStream = openFileOutput(fileName, MODE_PRIVATE)
           inputStream.copyTo(outputStream)
           inputStream.close()
           outputStream.close()
           android.util.Log.d("NoteEditor", "Imagen guardada como: $fileName")
           fileName
       } catch (e: Exception) {
           android.util.Log.e("NoteEditor", "Error al copiar imagen", e)
           null
       }
   }
   ```
2. Verificar en Logcat que el archivo se guarda correctamente.
3. Si el emulador no tiene imágenes, agregar algunas: arrastrar un archivo `.jpg` al emulador o usar `adb push imagen.jpg /sdcard/Pictures/`.
4. Verificar en `NoteDetailActivity` que se usa `openFileInput(fileName)` (almacenamiento interno) y no una ruta de archivo diferente.

---

### Problema 2: La app se cierra con crash al reabrir después de guardar notas (JsonSyntaxException)

**Síntomas:** La app funciona correctamente mientras está abierta, pero al cerrarla completamente y reabrirla, se produce un crash inmediato en `MainActivity.onCreate()`. En Logcat aparece: `com.google.gson.JsonSyntaxException: java.lang.IllegalStateException: Expected BEGIN_ARRAY but was STRING`.

**Causa:** La serialización JSON en SharedPreferences se corrompió. Esto puede ocurrir si se modificó manualmente el modelo `Note` (agregando o eliminando propiedades) después de haber guardado notas con la estructura anterior. Gson no puede deserializar el JSON antiguo con la nueva estructura de la clase.

**Solución:**
1. Limpiar los datos de la app en el emulador: **Settings > Apps > NotaKMP > Storage > Clear Data**. Alternativamente, desde la terminal:
   ```bash
   adb shell pm clear com.cursokotlin.android.notakmp
   ```
2. Si el problema persiste después de limpiar datos, verificar que el método `getAllNotes()` tiene el bloque `try-catch` que devuelve `emptyList()` en caso de error de deserialización (ya incluido en la implementación de la Fase 4).
3. Para prevenir este problema en el futuro, se puede agregar una versión del esquema en SharedPreferences:
   ```kotlin
   companion object {
       private const val PREFS_NAME = "notakmp_prefs"
       private const val KEY_NOTES = "notes_list"
       private const val KEY_SCHEMA_VERSION = "schema_version"
       private const val CURRENT_SCHEMA_VERSION = 1
   }
   ```
   Y al inicializar, verificar si la versión cambió para limpiar datos incompatibles.

---

## 9. Limpieza

Al finalizar el laboratorio y la entrega del proyecto:

1. **Cerrar los AVDs** que no se estén utilizando para liberar recursos del sistema:
   - En Android Studio: **Device Manager > ▼ (menú del AVD) > Stop**.

2. **Cerrar el proyecto en Android Studio** si no se continuará trabajando:
   - **File > Close Project**.

3. **No eliminar el directorio del proyecto.** El código fuente en `C:\AndroidCursoBasico\Lab7\NotaKMP\` (o `~/AndroidCursoBasico/Lab7/NotaKMP/`) debe conservarse como referencia del curso y como parte de la entrega final.

4. **Verificar que el repositorio Git está limpio:**

```bash
cd C:\AndroidCursoBasico\Lab7\NotaKMP
git status
```

Debe mostrar `nothing to commit, working tree clean`. Si hay archivos sin commit, agregarlos y hacer un commit final.

---

## 10. Resumen

### Lo que se logró en este laboratorio

En este proyecto integrador se completaron las seis fases del desarrollo de una aplicación Android real:

| Fase | Duración | Entregable |
|------|----------|------------|
| 1. Planeación | 30 min | `PLANNING.md` con alcance, pantallas y tecnologías |
| 2. Pantallas y navegación | 60 min | 3 Activities con layouts XML, RecyclerView, Intents y ViewBinding |
| 3. Multimedia | 30 min | Selección de imágenes desde galería con `ActivityResultLauncher`, almacenamiento interno, MediaPlayer opcional |
| 4. Persistencia | 30 min | SharedPreferences + Gson para serialización de notas |
| 5. Pruebas | 30 min | `TESTING.md` con 20 pruebas ejecutadas en AVD API 36 y API 30 |
| 6. Presentación | 36 min | APK generado, `README.md`, presentación en vivo |

### Conceptos clave aplicados

- **Planeación con MVP:** Definir el alcance mínimo viable antes de codificar evita la parálisis por exceso de funcionalidades.
- **Arquitectura por paquetes:** Separar `model`, `adapter` y `storage` mantiene el código organizado y mantenible.
- **ViewBinding:** Elimina `findViewById` y proporciona seguridad de tipos en tiempo de compilación.
- **ActivityResultLauncher:** Reemplaza el obsoleto `startActivityForResult` con un API moderna y type-safe.
- **Persistencia con SharedPreferences + Gson:** Solución pragmática para datos estructurados simples sin la complejidad de Room.
- **Ciclo de vida:** `onSaveInstanceState` preserva datos del formulario durante rotación; `onDestroy` libera recursos multimedia.
- **Pruebas en múltiples APIs:** Validar en `minSdk` (API 30) garantiza compatibilidad real con el rango de dispositivos declarado.

### Recursos adicionales

- [Guía oficial: Fundamentos de la arquitectura de apps
