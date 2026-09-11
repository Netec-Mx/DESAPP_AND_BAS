# Laboratorio 6 — Persistencia local de preferencias y datos

## 1. Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 144 minutos |
| **Complejidad** | Media |
| **Nivel Bloom** | Aplicar |
| **Proyecto** | `Lab6/PersistenciaLocal` |
| **Paquete base** | `com.cursokotlin.android.persistencialocal` |

---

## 2. Descripción General

En este laboratorio implementarás los cuatro mecanismos principales de persistencia local en Android dentro de un único proyecto: **SharedPreferences** para datos clave-valor síncronos, **Jetpack DataStore** como alternativa asíncrona basada en coroutines y Flow, **almacenamiento interno/externo** mediante archivos de texto, y **Room** como base de datos local con entidades, DAOs y procesamiento de anotaciones KSP. Al finalizar, podrás comparar cada mecanismo y seleccionar el más adecuado según el tipo y volumen de datos que necesite tu aplicación.

---

## 3. Objetivos de Aprendizaje

- [ ] Implementar almacenamiento clave-valor con SharedPreferences para guardar y recuperar preferencias simples del usuario entre sesiones.
- [ ] Utilizar Jetpack DataStore (Preferences DataStore) con coroutines y Flow como alternativa moderna y asíncrona a SharedPreferences.
- [ ] Leer y escribir archivos de texto en el almacenamiento interno del dispositivo usando las APIs `openFileOutput` / `openFileInput`.
- [ ] Crear una base de datos local con Room definiendo `@Entity`, `@Dao` y `@Database`, y verificar la persistencia de registros estructurados.
- [ ] Comparar los distintos mecanismos de persistencia y justificar cuál es más adecuado según el escenario.

---

## 4. Prerrequisitos

### Conocimientos previos

| Tema | Nivel requerido |
|---|---|
| Laboratorios 1–5 del curso completados | Obligatorio |
| Activities, Intents y ciclo de vida | Intermedio |
| Layouts XML y Views básicas | Intermedio |
| Kotlin: `suspend`, `CoroutineScope`, `Flow` (conceptos básicos) | Básico |
| ViewModel básico | Básico |

### Acceso y herramientas

| Recurso | Detalle |
|---|---|
| Android Studio | Quail 3 — 2026.1.3 Patch 1 |
| AVDs configurados | API 30, API 36 (principal) |
| Conexión a Internet | Para descarga de dependencias Gradle |
| Directorio de trabajo | `C:\AndroidCursoBasico\` (Windows) o `~/AndroidCursoBasico/` (macOS/Linux) |

---

## 5. Entorno del Laboratorio

### Hardware mínimo

| Componente | Especificación |
|---|---|
| CPU | Intel Core i5 8.ª gen. / AMD Ryzen 5 (VT-x/AMD-V habilitado) |
| RAM | 16 GB mínimo |
| Disco | 50 GB libres en SSD |
| Pantalla | 1280×800 mínimo (1920×1080 recomendado) |

### Software requerido

| Herramienta | Versión |
|---|---|
| Android Studio | Quail 3 (2026.1.3 Patch 1) |
| Kotlin | 2.2.10 |
| AGP | 9.3.2 |
| Gradle | 8.14.1 |
| JDK | 11 (`JavaVersion.VERSION_11`) |
| compileSdk / targetSdk | 37 |
| minSdk | 30 |
| Room | 2.8.4 |
| KSP | 2.2.10-1.0.28 |
| DataStore Preferences | 1.1.4 |
| Core KTX | 1.19.0 |
| Lifecycle Runtime KTX | 2.6.1 |

### Verificación rápida del entorno

Abre una terminal integrada en Android Studio y ejecuta:

```bash
java -version
```

**Salida esperada** (puede variar el build):

```
openjdk version "11.0.x" ...
```

Verifica también que los AVDs API 30 y API 36 estén disponibles en **Device Manager** de Android Studio.

---

## 6. Instrucciones Paso a Paso

---

### Paso 1 — Crear el proyecto base

**Objetivo:** Crear un proyecto Android con la plantilla correcta y la estructura de directorios del curso.

**Instrucciones:**

1. Abre Android Studio Quail 3.

2. Selecciona **File → New → New Project**.

3. En la lista de plantillas, selecciona **Empty Views Activity** (no "Empty Activity").

4. Configura los campos del asistente:

   | Campo | Valor |
   |---|---|
   | Name | `PersistenciaLocal` |
   | Package name | `com.cursokotlin.android.persistencialocal` |
   | Save location | `C:\AndroidCursoBasico\Lab6\PersistenciaLocal` (Windows) o `~/AndroidCursoBasico/Lab6/PersistenciaLocal` (macOS/Linux) |
   | Language | Kotlin |
   | Minimum SDK | API 30: Android 11.0 (R) |
   | Build configuration language | Kotlin DSL (build.gradle.kts) |

5. Haz clic en **Finish** y espera a que Gradle sincronice completamente.

**Verificación:**

- El proyecto se abre sin errores en la barra de estado.
- Existe el archivo `app/src/main/res/layout/activity_main.xml`.
- Existe `app/src/main/java/com/cursokotlin/android/persistencialocal/MainActivity.kt`.

---

### Paso 2 — Configurar el catálogo de versiones `libs.versions.toml`

**Objetivo:** Centralizar todas las dependencias con versiones fijas en el catálogo de versiones del proyecto.

**Instrucciones:**

1. Abre el archivo `gradle/libs.versions.toml` en la raíz del proyecto.

2. Reemplaza todo su contenido con lo siguiente:

```toml
[versions]
agp = "9.3.2"
kotlin = "2.2.10"
ksp = "2.2.10-1.0.28"
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
room = "2.8.4"
datastore = "1.1.4"
coroutines = "1.10.2"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }

# DataStore
androidx-datastore-preferences = { group = "androidx.datastore", name = "datastore-preferences", version.ref = "datastore" }

# Room
androidx-room-runtime = { group = "androidx.room", name = "room-runtime", version.ref = "room" }
androidx-room-ktx = { group = "androidx.room", name = "room-ktx", version.ref = "room" }
androidx-room-compiler = { group = "androidx.room", name = "room-compiler", version.ref = "room" }

# Coroutines
kotlinx-coroutines-android = { group = "org.jetbrains.kotlinx", name = "kotlinx-coroutines-android", version.ref = "coroutines" }

# Testing
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitExt" }
androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
ksp = { id = "com.google.devtools.ksp", version.ref = "ksp" }
```

3. Guarda el archivo.

**Verificación:**

- No aparecen errores de sintaxis en el archivo TOML.

---

### Paso 3 — Configurar los archivos Gradle del proyecto

**Objetivo:** Aplicar los plugins y dependencias correctas en los archivos `build.gradle.kts` del proyecto y del módulo `app`.

**Instrucciones:**

1. Abre el archivo **`build.gradle.kts` (Project: PersistenciaLocal)** y asegúrate de que contiene:

```kotlin
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    alias(libs.plugins.ksp) apply false
}
```

2. Abre el archivo **`build.gradle.kts` (Module: app)** y reemplaza su contenido completo con:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    alias(libs.plugins.ksp)
}

android {
    namespace = "com.cursokotlin.android.persistencialocal"
    compileSdk = 37

    defaultConfig {
        applicationId = "com.cursokotlin.android.persistencialocal"
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

    // DataStore Preferences
    implementation(libs.androidx.datastore.preferences)

    // Coroutines
    implementation(libs.kotlinx.coroutines.android)

    // Room
    implementation(libs.androidx.room.runtime)
    implementation(libs.androidx.room.ktx)
    ksp(libs.androidx.room.compiler)

    // Testing
    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
}
```

3. Haz clic en **Sync Now** en la barra amarilla que aparece en la parte superior del editor.

**Salida esperada:**

```
BUILD SUCCESSFUL in Xs
```

**Verificación:**

- La sincronización de Gradle finaliza sin errores.
- En la ventana **Build**, no hay advertencias de versiones incompatibles.

---

### Paso 4 — Configurar la navegación entre secciones (MainActivity)

**Objetivo:** Crear una pantalla principal con botones que permitan navegar a cada una de las cuatro secciones del laboratorio.

**Instrucciones:**

1. Abre `app/src/main/res/layout/activity_main.xml` y reemplaza su contenido con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Persistencia Local en Android"
        android:textSize="22sp"
        android:textStyle="bold"
        android:layout_marginBottom="32dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnSharedPrefs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sección 1: SharedPreferences"
        android:layout_marginBottom="12dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnDataStore"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sección 2: DataStore"
        android:layout_marginBottom="12dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnArchivos"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sección 3: Archivos Internos"
        android:layout_marginBottom="12dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnRoom"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sección 4: Room Database" />

</LinearLayout>
```

2. Abre `MainActivity.kt` y reemplaza su contenido con:

```kotlin
package com.cursokotlin.android.persistencialocal

import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.persistencialocal.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.btnSharedPrefs.setOnClickListener {
            startActivity(Intent(this, SharedPrefsActivity::class.java))
        }

        binding.btnDataStore.setOnClickListener {
            startActivity(Intent(this, DataStoreActivity::class.java))
        }

        binding.btnArchivos.setOnClickListener {
            startActivity(Intent(this, ArchivosActivity::class.java))
        }

        binding.btnRoom.setOnClickListener {
            startActivity(Intent(this, RoomActivity::class.java))
        }
    }
}
```

> **Nota:** Las Activities referenciadas aún no existen. Las crearemos en los pasos siguientes. El proyecto no compilará hasta completar el Paso 5.

**Verificación:**

- El archivo XML no muestra errores de sintaxis en el editor visual.
- El código Kotlin muestra errores de referencia esperados (clases no creadas aún).

---

### Paso 5 — SECCIÓN 1: SharedPreferences (~30 min)

**Objetivo:** Implementar una pantalla que permita guardar el nombre de usuario y una preferencia booleana (modo oscuro simulado) usando SharedPreferences, y recuperar los valores al reiniciar la app.

**Instrucciones:**

1. Crea el archivo de layout `app/src/main/res/layout/activity_shared_prefs.xml`:

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
        android:text="Sección 1: SharedPreferences"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginBottom="24dp" />

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Nombre de usuario"
        android:layout_marginBottom="16dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etNombreUsuario"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:layout_marginBottom="24dp">

        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Modo oscuro (simulado)"
            android:textSize="16sp" />

        <com.google.android.material.switchmaterial.SwitchMaterial
            android:id="@+id/switchModoOscuro"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnGuardar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Guardar Preferencias"
        android:layout_marginBottom="16dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnLimpiar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Limpiar Preferencias"
        style="@style/Widget.Material3.Button.OutlinedButton"
        android:layout_marginBottom="24dp" />

    <TextView
        android:id="@+id/tvEstado"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Estado: Sin datos guardados"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray" />

</LinearLayout>
```

2. Crea la clase `SharedPrefsActivity.kt` en el paquete principal:

```kotlin
package com.cursokotlin.android.persistencialocal

import android.content.Context
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.persistencialocal.databinding.ActivitySharedPrefsBinding

class SharedPrefsActivity : AppCompatActivity() {

    private lateinit var binding: ActivitySharedPrefsBinding

    companion object {
        const val PREFS_NAME = "user_preferences"
        const val KEY_NOMBRE = "nombre_usuario"
        const val KEY_MODO_OSCURO = "modo_oscuro"
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivitySharedPrefsBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Recuperar preferencias guardadas al abrir la pantalla
        cargarPreferencias()

        binding.btnGuardar.setOnClickListener {
            guardarPreferencias()
        }

        binding.btnLimpiar.setOnClickListener {
            limpiarPreferencias()
        }
    }

    private fun guardarPreferencias() {
        val prefs = getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val nombre = binding.etNombreUsuario.text.toString()
        val modoOscuro = binding.switchModoOscuro.isChecked

        prefs.edit()
            .putString(KEY_NOMBRE, nombre)
            .putBoolean(KEY_MODO_OSCURO, modoOscuro)
            .apply()

        binding.tvEstado.text = "Estado: Preferencias guardadas ✓\n" +
                "Nombre: $nombre\nModo oscuro: $modoOscuro"
    }

    private fun cargarPreferencias() {
        val prefs = getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val nombre = prefs.getString(KEY_NOMBRE, "") ?: ""
        val modoOscuro = prefs.getBoolean(KEY_MODO_OSCURO, false)

        binding.etNombreUsuario.setText(nombre)
        binding.switchModoOscuro.isChecked = modoOscuro

        if (nombre.isNotEmpty()) {
            binding.tvEstado.text = "Estado: Datos recuperados de SharedPreferences\n" +
                    "Nombre: $nombre\nModo oscuro: $modoOscuro"
        }
    }

    private fun limpiarPreferencias() {
        val prefs = getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        prefs.edit().clear().apply()

        binding.etNombreUsuario.setText("")
        binding.switchModoOscuro.isChecked = false
        binding.tvEstado.text = "Estado: Preferencias eliminadas"
    }
}
```

3. Registra la Activity en `AndroidManifest.xml`. Abre el archivo y añade dentro de `<application>`, **antes** de la etiqueta de cierre `</application>`:

```xml
<activity android:name=".SharedPrefsActivity"
    android:exported="false" />
```

> **Importante:** Cada nueva Activity que crees en este laboratorio debe registrarse en el manifiesto. Repetiremos esta indicación en cada sección.

4. Compila el proyecto con **Build → Make Project** (Ctrl+F9 / Cmd+F9).

> **Nota:** La compilación fallará porque las otras tres Activities aún no existen. Puedes crear archivos vacíos temporales o esperar al final del Paso 8 para la primera compilación completa. Si prefieres compilar ahora, comenta temporalmente los tres listeners restantes en `MainActivity.kt`.

**Salida esperada al ejecutar (después de crear todas las Activities):**

- Al abrir "Sección 1: SharedPreferences", los campos aparecen vacíos.
- Al escribir un nombre, activar el switch y presionar "Guardar Preferencias", el texto de estado muestra los valores guardados.
- Al cerrar la app completamente (desde recientes) y reabrirla, los valores persisten en los campos.

**Verificación:**

- Escribe "María López" en el campo de nombre, activa el switch y guarda.
- Cierra la app desde el menú de recientes del emulador.
- Reabre la app y navega a Sección 1: el campo muestra "María López" y el switch está activado.
- Presiona "Limpiar Preferencias" y verifica que ambos campos se reinician.

---

### Paso 6 — SECCIÓN 2: DataStore Preferences (~35 min)

**Objetivo:** Implementar la misma funcionalidad de la Sección 1 usando Jetpack DataStore con coroutines y Flow, comparando el enfoque asíncrono con SharedPreferences.

**Instrucciones:**

1. Crea el archivo `SettingsDataStore.kt` en el paquete principal. Este será el singleton de DataStore:

```kotlin
package com.cursokotlin.android.persistencialocal

import android.content.Context
import androidx.datastore.core.DataStore
import androidx.datastore.preferences.core.Preferences
import androidx.datastore.preferences.core.booleanPreferencesKey
import androidx.datastore.preferences.core.edit
import androidx.datastore.preferences.core.stringPreferencesKey
import androidx.datastore.preferences.preferencesDataStore
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map

// Extensión de propiedad que crea un singleton de DataStore asociado al Context
val Context.settingsDataStore: DataStore<Preferences> by preferencesDataStore(
    name = "settings_datastore"
)

object SettingsDataStore {

    // Claves tipadas
    val KEY_NOMBRE = stringPreferencesKey("ds_nombre_usuario")
    val KEY_MODO_OSCURO = booleanPreferencesKey("ds_modo_oscuro")

    // Lectura reactiva del nombre
    fun getNombre(context: Context): Flow<String> {
        return context.settingsDataStore.data.map { preferences ->
            preferences[KEY_NOMBRE] ?: ""
        }
    }

    // Lectura reactiva del modo oscuro
    fun getModoOscuro(context: Context): Flow<Boolean> {
        return context.settingsDataStore.data.map { preferences ->
            preferences[KEY_MODO_OSCURO] ?: false
        }
    }

    // Escritura (función suspend — debe llamarse desde una coroutine)
    suspend fun guardarPreferencias(
        context: Context,
        nombre: String,
        modoOscuro: Boolean
    ) {
        context.settingsDataStore.edit { preferences ->
            preferences[KEY_NOMBRE] = nombre
            preferences[KEY_MODO_OSCURO] = modoOscuro
        }
    }

    // Limpiar todas las preferencias
    suspend fun limpiarPreferencias(context: Context) {
        context.settingsDataStore.edit { preferences ->
            preferences.clear()
        }
    }
}
```

2. Crea el layout `app/src/main/res/layout/activity_data_store.xml`:

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
        android:text="Sección 2: DataStore Preferences"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginBottom="24dp" />

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Nombre de usuario (DataStore)"
        android:layout_marginBottom="16dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etNombreDS"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:layout_marginBottom="24dp">

        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Modo oscuro (DataStore)"
            android:textSize="16sp" />

        <com.google.android.material.switchmaterial.SwitchMaterial
            android:id="@+id/switchModoOscuroDS"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnGuardarDS"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Guardar con DataStore"
        android:layout_marginBottom="16dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnLimpiarDS"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Limpiar DataStore"
        style="@style/Widget.Material3.Button.OutlinedButton"
        android:layout_marginBottom="24dp" />

    <TextView
        android:id="@+id/tvEstadoDS"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Estado: Esperando datos de DataStore..."
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray" />

</LinearLayout>
```

3. Crea la clase `DataStoreActivity.kt`:

```kotlin
package com.cursokotlin.android.persistencialocal

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.lifecycle.lifecycleScope
import com.cursokotlin.android.persistencialocal.databinding.ActivityDataStoreBinding
import kotlinx.coroutines.flow.first
import kotlinx.coroutines.launch

class DataStoreActivity : AppCompatActivity() {

    private lateinit var binding: ActivityDataStoreBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityDataStoreBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Observar cambios reactivos desde DataStore
        observarPreferencias()

        binding.btnGuardarDS.setOnClickListener {
            guardarPreferencias()
        }

        binding.btnLimpiarDS.setOnClickListener {
            limpiarPreferencias()
        }
    }

    private fun observarPreferencias() {
        // Recolectar el Flow del nombre
        lifecycleScope.launch {
            SettingsDataStore.getNombre(this@DataStoreActivity).collect { nombre ->
                binding.etNombreDS.setText(nombre)
                actualizarEstado()
            }
        }

        // Recolectar el Flow del modo oscuro
        lifecycleScope.launch {
            SettingsDataStore.getModoOscuro(this@DataStoreActivity).collect { modoOscuro ->
                binding.switchModoOscuroDS.isChecked = modoOscuro
                actualizarEstado()
            }
        }
    }

    private fun guardarPreferencias() {
        val nombre = binding.etNombreDS.text.toString()
        val modoOscuro = binding.switchModoOscuroDS.isChecked

        lifecycleScope.launch {
            SettingsDataStore.guardarPreferencias(
                this@DataStoreActivity,
                nombre,
                modoOscuro
            )
            binding.tvEstadoDS.text = "Estado: Guardado con DataStore ✓\n" +
                    "Nombre: $nombre\nModo oscuro: $modoOscuro"
        }
    }

    private fun limpiarPreferencias() {
        lifecycleScope.launch {
            SettingsDataStore.limpiarPreferencias(this@DataStoreActivity)
            binding.tvEstadoDS.text = "Estado: DataStore limpiado"
        }
    }

    private suspend fun actualizarEstado() {
        val nombre = SettingsDataStore.getNombre(this).first()
        val modoOscuro = SettingsDataStore.getModoOscuro(this).first()
        if (nombre.isNotEmpty()) {
            binding.tvEstadoDS.text = "Estado: Datos de DataStore\n" +
                    "Nombre: $nombre\nModo oscuro: $modoOscuro"
        }
    }
}
```

4. Registra la Activity en `AndroidManifest.xml`:

```xml
<activity android:name=".DataStoreActivity"
    android:exported="false" />
```

**Salida esperada:**

- Al abrir la Sección 2, si no hay datos previos, los campos aparecen vacíos.
- Al guardar datos y cerrar/reabrir la app, los valores se recuperan automáticamente gracias a los Flows.
- La escritura es asíncrona: no bloquea el hilo principal.

**Verificación:**

- Guarda "Carlos Ruiz" con modo oscuro activado.
- Cierra la app completamente y reabre → los datos persisten.
- Observa en Logcat que no hay advertencias de operaciones en hilo principal (a diferencia de `commit()` en SharedPreferences).

---

### Paso 7 — SECCIÓN 3: Archivos en almacenamiento interno (~35 min)

**Objetivo:** Implementar lectura y escritura de archivos de texto en el almacenamiento interno del dispositivo para mantener un log de actividad persistente.

**Instrucciones:**

1. Crea el layout `app/src/main/res/layout/activity_archivos.xml`:

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
        android:text="Sección 3: Archivos Internos"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginBottom="24dp" />

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Escribir entrada de log"
        android:layout_marginBottom="16dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etEntradaLog"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginBottom="16dp">

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnEscribir"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Escribir"
            android:layout_marginEnd="8dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnLeer"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Leer"
            android:layout_marginStart="8dp" />
    </LinearLayout>

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnEliminarArchivo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Eliminar Archivo"
        style="@style/Widget.Material3.Button.OutlinedButton"
        android:layout_marginBottom="24dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Contenido del archivo:"
        android:textSize="16sp"
        android:textStyle="bold"
        android:layout_marginBottom="8dp" />

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

        <TextView
            android:id="@+id/tvContenidoArchivo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="(vacío)"
            android:textSize="14sp"
            android:fontFamily="monospace"
            android:background="#F5F5F5"
            android:padding="12dp" />
    </ScrollView>

    <TextView
        android:id="@+id/tvInfoArchivo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Ruta del archivo: ..."
        android:textSize="12sp"
        android:textColor="@android:color/darker_gray"
        android:layout_marginTop="8dp" />

</LinearLayout>
```

2. Crea la clase `ArchivosActivity.kt`:

```kotlin
package com.cursokotlin.android.persistencialocal

import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.persistencialocal.databinding.ActivityArchivosBinding
import java.io.File
import java.io.FileNotFoundException
import java.text.SimpleDateFormat
import java.util.Date
import java.util.Locale

class ArchivosActivity : AppCompatActivity() {

    private lateinit var binding: ActivityArchivosBinding

    companion object {
        const val NOMBRE_ARCHIVO = "log_actividad.txt"
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityArchivosBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Mostrar la ruta del almacenamiento interno
        val rutaArchivo = File(filesDir, NOMBRE_ARCHIVO).absolutePath
        binding.tvInfoArchivo.text = "Ruta: $rutaArchivo"

        binding.btnEscribir.setOnClickListener {
            escribirArchivo()
        }

        binding.btnLeer.setOnClickListener {
            leerArchivo()
        }

        binding.btnEliminarArchivo.setOnClickListener {
            eliminarArchivo()
        }

        // Cargar contenido existente al abrir
        leerArchivo()
    }

    private fun escribirArchivo() {
        val entrada = binding.etEntradaLog.text.toString().trim()
        if (entrada.isEmpty()) {
            Toast.makeText(this, "Escribe algo antes de guardar", Toast.LENGTH_SHORT).show()
            return
        }

        val timestamp = SimpleDateFormat("yyyy-MM-dd HH:mm:ss", Locale.getDefault())
            .format(Date())
        val lineaLog = "[$timestamp] $entrada\n"

        // MODE_APPEND agrega al final del archivo en lugar de sobrescribir
        openFileOutput(NOMBRE_ARCHIVO, MODE_APPEND).use { outputStream ->
            outputStream.write(lineaLog.toByteArray())
        }

        binding.etEntradaLog.setText("")
        Toast.makeText(this, "Entrada guardada en archivo", Toast.LENGTH_SHORT).show()

        // Refrescar la vista
        leerArchivo()
    }

    private fun leerArchivo() {
        try {
            openFileInput(NOMBRE_ARCHIVO).use { inputStream ->
                val contenido = inputStream.bufferedReader().readText()
                if (contenido.isNotEmpty()) {
                    binding.tvContenidoArchivo.text = contenido
                } else {
                    binding.tvContenidoArchivo.text = "(archivo vacío)"
                }
            }
        } catch (e: FileNotFoundException) {
            binding.tvContenidoArchivo.text = "(archivo no encontrado — escribe algo primero)"
        }
    }

    private fun eliminarArchivo() {
        val archivo = File(filesDir, NOMBRE_ARCHIVO)
        if (archivo.exists()) {
            archivo.delete()
            binding.tvContenidoArchivo.text = "(archivo eliminado)"
            Toast.makeText(this, "Archivo eliminado", Toast.LENGTH_SHORT).show()
        } else {
            Toast.makeText(this, "No hay archivo para eliminar", Toast.LENGTH_SHORT).show()
        }
    }
}
```

3. Registra la Activity en `AndroidManifest.xml`:

```xml
<activity android:name=".ArchivosActivity"
    android:exported="false" />
```

**Salida esperada:**

- Al escribir texto y presionar "Escribir", se agrega una línea con timestamp al archivo.
- Al presionar "Leer", se muestra todo el contenido acumulado del archivo.
- La ruta del archivo se muestra en la parte inferior (ej: `/data/data/com.cursokotlin.android.persistencialocal/files/log_actividad.txt`).
- Al cerrar y reabrir la app, el contenido persiste.

**Verificación:**

- Escribe tres entradas diferentes y verifica que todas aparecen con su timestamp.
- Cierra la app completamente, reabre y navega a Sección 3 → las entradas persisten.
- Presiona "Eliminar Archivo" y verifica que el contenido desaparece.
- Intenta leer nuevamente → muestra "(archivo no encontrado)".

---

### Paso 8 — SECCIÓN 4: Introducción a Room (~44 min)

**Objetivo:** Crear una base de datos local con Room, definiendo una entidad `Nota`, un DAO con operaciones CRUD básicas y una clase Database, y construir una pantalla para agregar y listar notas.

**Instrucciones:**

#### 8a — Definir la entidad

1. Crea el paquete `com.cursokotlin.android.persistencialocal.room` (clic derecho en el paquete principal → New → Package).

2. Dentro de ese paquete, crea el archivo `Nota.kt`:

```kotlin
package com.cursokotlin.android.persistencialocal.room

import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "notas")
data class Nota(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val titulo: String,
    val contenido: String
)
```

#### 8b — Definir el DAO

3. En el mismo paquete `room`, crea el archivo `NotaDao.kt`:

```kotlin
package com.cursokotlin.android.persistencialocal.room

import androidx.room.Dao
import androidx.room.Insert
import androidx.room.Query

@Dao
interface NotaDao {

    @Insert
    suspend fun insertar(nota: Nota)

    @Query("SELECT * FROM notas ORDER BY id DESC")
    suspend fun obtenerTodas(): List<Nota>

    @Query("DELETE FROM notas WHERE id = :notaId")
    suspend fun eliminarPorId(notaId: Int)

    @Query("DELETE FROM notas")
    suspend fun eliminarTodas()
}
```

#### 8c — Definir la base de datos

4. En el mismo paquete `room`, crea el archivo `NotaDatabase.kt`:

```kotlin
package com.cursokotlin.android.persistencialocal.room

import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase

@Database(entities = [Nota::class], version = 1, exportSchema = false)
abstract class NotaDatabase : RoomDatabase() {

    abstract fun notaDao(): NotaDao

    companion object {
        @Volatile
        private var INSTANCE: NotaDatabase? = null

        fun obtenerInstancia(context: Context): NotaDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    NotaDatabase::class.java,
                    "notas_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

#### 8d — Crear el layout de la pantalla Room

5. Crea el layout `app/src/main/res/layout/activity_room.xml`:

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
        android:text="Sección 4: Room Database"
        android:textSize="20sp"
        android:textStyle="bold"
        android:layout_marginBottom="16dp" />

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Título de la nota"
        android:layout_marginBottom="8dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etTitulo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </com.google.android.material.textfield.TextInputLayout>

    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Contenido de la nota"
        android:layout_marginBottom="16dp">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etContenido"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:minLines="2" />
    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginBottom="16dp">

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnAgregarNota"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Agregar Nota"
            android:layout_marginEnd="8dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnEliminarTodas"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Eliminar Todas"
            style="@style/Widget.Material3.Button.OutlinedButton"
            android:layout_marginStart="8dp" />
    </LinearLayout>

    <TextView
        android:id="@+id/tvContadorNotas"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Notas: 0"
        android:textSize="14sp"
        android:textStyle="bold"
        android:layout_marginBottom="8dp" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvNotas"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

</LinearLayout>
```

#### 8e — Crear el layout del item de nota

6. Crea el archivo `app/src/main/res/layout/item_nota.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginBottom="8dp"
    app:cardElevation="2dp"
    app:cardCornerRadius="8dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="16dp"
        android:gravity="center_vertical">

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:orientation="vertical">

            <TextView
                android:id="@+id/tvNotaTitulo"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textSize="16sp"
                android:textStyle="bold" />

            <TextView
                android:id="@+id/tvNotaContenido"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textSize="14sp"
                android:layout_marginTop="4dp"
                android:textColor="@android:color/darker_gray" />

            <TextView
                android:id="@+id/tvNotaId"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textSize="12sp"
                android:textColor="@android:color/darker_gray"
                android:layout_marginTop="4dp" />
        </LinearLayout>

        <ImageButton
            android:id="@+id/btnEliminarNota"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:src="@android:drawable/ic_menu_delete"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:contentDescription="Eliminar nota" />
    </LinearLayout>

</com.google.android.material.card.MaterialCardView>
```

#### 8f — Crear el Adapter del RecyclerView

7. Crea el archivo `NotaAdapter.kt` en el paquete `room`:

```kotlin
package com.cursokotlin.android.persistencialocal.room

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.cursokotlin.android.persistencialocal.databinding.ItemNotaBinding

class NotaAdapter(
    private var notas: List<Nota>,
    private val onEliminar: (Nota) -> Unit
) : RecyclerView.Adapter<NotaAdapter.NotaViewHolder>() {

    inner class NotaViewHolder(
        private val binding: ItemNotaBinding
    ) : RecyclerView.ViewHolder(binding.root) {

        fun bind(nota: Nota) {
            binding.tvNotaTitulo.text = nota.titulo
            binding.tvNotaContenido.text = nota.contenido
            binding.tvNotaId.text = "ID: ${nota.id}"
            binding.btnEliminarNota.setOnClickListener {
                onEliminar(nota)
            }
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): NotaViewHolder {
        val binding = ItemNotaBinding.inflate(
            LayoutInflater.from(parent.context), parent, false
        )
        return NotaViewHolder(binding)
    }

    override fun onBindViewHolder(holder: NotaViewHolder, position: Int) {
        holder.bind(notas[position])
    }

    override fun getItemCount(): Int = notas.size

    fun actualizarNotas(nuevasNotas: List<Nota>) {
        notas = nuevasNotas
        notifyDataSetChanged()
    }
}
```

#### 8g — Crear la Activity de Room

8. Crea la clase `RoomActivity.kt` en el paquete principal:

```kotlin
package com.cursokotlin.android.persistencialocal

import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.lifecycle.lifecycleScope
import androidx.recyclerview.widget.LinearLayoutManager
import com.cursokotlin.android.persistencialocal.databinding.ActivityRoomBinding
import com.cursokotlin.android.persistencialocal.room.Nota
import com.cursokotlin.android.persistencialocal.room.NotaAdapter
import com.cursokotlin.android.persistencialocal.room.NotaDatabase
import kotlinx.coroutines.launch

class RoomActivity : AppCompatActivity() {

    private lateinit var binding: ActivityRoomBinding
    private lateinit var database: NotaDatabase
    private lateinit var adapter: NotaAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityRoomBinding.inflate(layoutInflater)
        setContentView(binding.root)

        database = NotaDatabase.obtenerInstancia(this)

        // Configurar RecyclerView
        adapter = NotaAdapter(emptyList()) { nota ->
            eliminarNota(nota)
        }
        binding.rvNotas.layoutManager = LinearLayoutManager(this)
        binding.rvNotas.adapter = adapter

        binding.btnAgregarNota.setOnClickListener {
            agregarNota()
        }

        binding.btnEliminarTodas.setOnClickListener {
            eliminarTodas()
        }

        // Cargar notas existentes
        cargarNotas()
    }

    private fun agregarNota() {
        val titulo = binding.etTitulo.text.toString().trim()
        val contenido = binding.etContenido.text.toString().trim()

        if (titulo.isEmpty()) {
            Toast.makeText(this, "El título es obligatorio", Toast.LENGTH_SHORT).show()
            return
        }

        lifecycleScope.launch {
            val nota = Nota(titulo = titulo, contenido = contenido)
            database.notaDao().insertar(nota)

            // Limpiar campos
            binding.etTitulo.setText("")
            binding.etContenido.setText("")

            Toast.makeText(this@RoomActivity, "Nota agregada", Toast.LENGTH_SHORT).show()
            cargarNotas()
        }
    }

    private fun cargarNotas() {
        lifecycleScope.launch {
            val notas = database.notaDao().obtenerTodas()
            adapter.actualizarNotas(notas)
            binding.tvContadorNotas.text = "Notas: ${notas.size}"
        }
    }

    private fun eliminarNota(nota: Nota) {
        lifecycleScope.launch {
            database.notaDao().eliminarPorId(nota.id)
            Toast.makeText(
                this@RoomActivity,
                "Nota '${nota.titulo}' eliminada",
                Toast.LENGTH_SHORT
            ).show()
            cargarNotas()
        }
    }

    private fun eliminarTodas() {
        lifecycleScope.launch {
            database.notaDao().eliminarTodas()
            Toast.makeText(this@RoomActivity, "Todas las notas eliminadas", Toast.LENGTH_SHORT)
                .show()
            cargarNotas()
        }
    }
}
```

9. Registra la Activity en `AndroidManifest.xml`:

```xml
<activity android:name=".RoomActivity"
    android:exported="false" />
```

**Salida esperada:**

- Al abrir la Sección 4, el RecyclerView está vacío con el contador "Notas: 0".
- Al agregar una nota con título y contenido, aparece como una tarjeta en la lista.
- Cada tarjeta muestra el título, contenido, ID y un botón para eliminar.
- El contador se actualiza dinámicamente.

**Verificación:**

- Agrega tres notas con títulos distintos ("Nota 1", "Nota 2", "Nota 3").
- Verifica que el contador muestra "Notas: 3".
- Elimina "Nota 2" usando el botón de eliminar → el contador cambia a "Notas: 2".
- Cierra la app completamente desde recientes.
- Reabre la app y navega a Sección 4 → las notas "Nota 1" y "Nota 3" persisten.

---

### Paso 9 — Compilar y ejecutar el proyecto completo

**Objetivo:** Verificar que las cuatro secciones compilan correctamente y funcionan en el AVD.

**Instrucciones:**

1. Verifica que el `AndroidManifest.xml` completo contiene las cuatro Activities registradas. La sección `<application>` debe incluir:

```xml
<activity android:name=".SharedPrefsActivity"
    android:exported="false" />
<activity android:name=".DataStoreActivity"
    android:exported="false" />
<activity android:name=".ArchivosActivity"
    android:exported="false" />
<activity android:name=".RoomActivity"
    android:exported="false" />
```

2. Selecciona el AVD **API 36** en la barra de herramientas.

3. Ejecuta la app con **Run → Run 'app'** (Shift+F10).

4. En la pantalla principal, prueba cada sección en orden:
   - **Sección 1:** Guarda nombre y modo oscuro → cierra y reabre → verifica persistencia.
   - **Sección 2:** Guarda datos con DataStore → cierra y reabre → verifica persistencia.
   - **Sección 3:** Escribe varias entradas de log → cierra y reabre → verifica que el archivo persiste.
   - **Sección 4:** Agrega notas → elimina una → cierra y reabre → verifica persistencia.

**Salida esperada en Build:**

```
BUILD SUCCESSFUL in Xs
```

**Verificación final:**

- Las cuatro secciones funcionan sin crashes.
- Los datos persisten después de cerrar y reabrir la app en todas las secciones.

---

### Paso 10 — Validar compatibilidad con minSdk (AVD API 30)

**Objetivo:** Confirmar que la app funciona correctamente en el nivel de API mínimo definido.

**Instrucciones:**

1. En la barra de herramientas, cambia el dispositivo de ejecución al AVD **API 30**.

2. Ejecuta la app con **Run → Run 'app'**.

3. Repite las pruebas básicas de cada sección:
   - SharedPreferences: guardar y recuperar.
   - DataStore: guardar y recuperar.
   - Archivos: escribir y leer.
   - Room: agregar y listar notas.

**Salida esperada:**

- La app se instala y ejecuta sin errores en API 30.
- Todas las funcionalidades de persistencia operan correctamente.

**Verificación:**

- No aparecen errores de tipo `NoClassDefFoundError` ni `NoSuchMethodError` en Logcat.
- El comportamiento es idéntico al AVD API 36.

---

## 7. Validación y Pruebas

### Prueba integral de persistencia

Ejecuta la siguiente secuencia completa en el AVD API 36:

| # | Acción | Resultado esperado |
|---|---|---|
| 1 | Sección 1: Guardar "Ana" con modo oscuro ON | Estado muestra "Preferencias guardadas ✓" |
| 2 | Sección 2: Guardar "Pedro" con modo oscuro OFF | Estado muestra "Guardado con DataStore ✓" |
| 3 | Sección 3: Escribir "Inicio de sesión" y "Cierre de sesión" | Ambas entradas aparecen con timestamps |
| 4 | Sección 4: Agregar 3 notas y eliminar 1 | Contador muestra "Notas: 2" |
| 5 | Cerrar la app completamente (swipe en recientes) | — |
| 6 | Reabrir la app | Pantalla principal se muestra |
| 7 | Sección 1: Verificar | "Ana" aparece, switch ON |
| 8 | Sección 2: Verificar | "Pedro" aparece, switch OFF |
| 9 | Sección 3: Verificar | Las dos entradas de log persisten |
| 10 | Sección 4: Verificar | 2 notas persisten |

### Verificación con Device File Explorer

1. En Android Studio, abre **View → Tool Windows → Device File Explorer**.
2. Navega a `/data/data/com.cursokotlin.android.persistencialocal/`:
   - `shared_prefs/user_preferences.xml` → Verifica las claves `nombre_usuario` y `modo_oscuro`.
   - `files/log_actividad.txt` → Verifica el contenido del log.
   - `files/datastore/settings_datastore.preferences_pb` → Archivo binario de DataStore (no legible directamente).
   - `databases/notas_database` → Archivo de base de datos Room.

---

## 8. Solución de Problemas

### Problema 1: Error de compilación KSP con Room — "Cannot find implementation for database"

**Síntomas:**

```
error: Cannot find implementation for com.cursokotlin.android.persistencialocal.room.NotaDatabase.
```

O bien la app compila pero crashea en tiempo de ejecución con:

```
java.lang.RuntimeException: Cannot find implementation for NotaDatabase
```

**Causa:**

El plugin KSP no está aplicado correctamente o la versión de KSP no es compatible con la versión de Kotlin. Room necesita KSP para generar el código de implementación del DAO y la Database en tiempo de compilación.

**Solución:**

1. Verifica que el plugin KSP está declarado en `libs.versions.toml`:
   ```toml
   ksp = "2.2.10-1.0.28"
   ```
   La primera parte (`2.2.10`) **debe coincidir exactamente** con la versión de Kotlin.

2. Verifica que el plugin está en el bloque `[plugins]` del TOML:
   ```toml
   ksp = { id = "com.google.devtools.ksp", version.ref = "ksp" }
   ```

3. Verifica que está aplicado en `build.gradle.kts` (Project):
   ```kotlin
   alias(libs.plugins.ksp) apply false
   ```

4. Verifica que está aplicado en `build.gradle.kts` (Module: app):
   ```kotlin
   alias(libs.plugins.ksp)
   ```

5. Verifica que la dependencia del compilador Room usa `ksp` (no `kapt` ni `annotationProcessor`):
   ```kotlin
   ksp(libs.androidx.room.compiler)
   ```

6. Ejecuta **Build → Clean Project** seguido de **Build → Rebuild Project**.

---

### Problema 2: DataStore lanza `IllegalStateException` — "There are multiple DataStores active for the same file"

**Síntomas:**

```
java.lang.IllegalStateException: There are multiple DataStores active for the same file: 
.../datastore/settings_datastore.preferences_pb. You should either maintain your DataStore 
as a singleton or confirm that there is no two DataStore's active on the same file.
```

La app crashea al abrir la pantalla de DataStore por segunda vez o al rotar el dispositivo.

**Causa:**

La extensión `preferencesDataStore` se declaró dentro de una clase o función en lugar de como propiedad de extensión de nivel superior (`top-level`). Esto provoca que se creen múltiples instancias de DataStore para el mismo archivo, lo cual está prohibido por la biblioteca.

**Solución:**

1. Verifica que la declaración del DataStore está **fuera de cualquier clase**, como propiedad de extensión de `Context` a nivel de archivo:

   ```kotlin
   // ✅ CORRECTO — declaración top-level
   val Context.settingsDataStore: DataStore<Preferences> by preferencesDataStore(
       name = "settings_datastore"
   )
   ```

2. **No** declares el DataStore dentro de una Activity, Fragment o companion object:

   ```kotlin
   // ❌ INCORRECTO — dentro de una clase
   class DataStoreActivity : AppCompatActivity() {
       val Context.settingsDataStore by preferencesDataStore(name = "settings_datastore")
   }
   ```

3. Si el error persiste después de corregir la ubicación, desinstala la app del emulador (**Settings → Apps → PersistenciaLocal → Uninstall**) y vuelve a ejecutar.

---

## 9. Limpieza

Una vez completado y verificado el laboratorio:

1. **Detén la ejecución** de la app en el emulador (botón Stop en Android Studio).

2. **No elimines el proyecto.** Este laboratorio es prerequisito del Laboratorio 7, donde integrarás al menos uno de estos mecanismos de persistencia en el proyecto final.

3. Si deseas liberar espacio en el emulador durante pruebas futuras, puedes desinstalar la app desde el AVD:

   ```
   Configuración → Apps → PersistenciaLocal → Desinstalar
   ```

   Esto eliminará todos los archivos de SharedPreferences, DataStore, almacenamiento interno y la base de datos Room asociados a la app.

4. **Opcional:** Para detener los AVDs y liberar RAM, cierra los emuladores desde el **Device Manager** de Android Studio.

---

## 10. Resumen

### Lo que implementaste

En este laboratorio construiste un proyecto Android completo con cuatro mecanismos de persistencia local:

| Mecanismo | Tipo de datos | API | Hilo |
|---|---|---|---|
| **SharedPreferences** | Clave-valor (primitivos) | Síncrona (`apply()` es async en disco) | Principal (lectura); async en disco (escritura con `apply()`) |
| **DataStore Preferences** | Clave-valor (tipado) | Asíncrona (coroutines + Flow) | Background (coroutines) |
| **Archivos internos** | Texto libre / binario | Síncrona (I/O streams) | Principal (requiere manejo manual para archivos grandes) |
| **Room** | Datos estructurados (relacional) | Asíncrona (suspend + coroutines) | Background (coroutines) |

### Criterios de selección

| Escenario | Mecanismo recomendado |
|---|---|
| Preferencias simples del usuario (tema, idioma, flags) | SharedPreferences o DataStore |
| Configuraciones con necesidad de reactividad (Flow) | DataStore Preferences |
| Logs, exportaciones de texto, archivos temporales | Almacenamiento interno/externo |
| Datos estructurados con relaciones y consultas | Room |

### Conceptos clave reforzados

- Las claves de SharedPreferences deben definirse como **constantes** para evitar errores tipográficos.
- DataStore es la alternativa moderna recomendada por Google; su singleton **debe** declararse a nivel de archivo.
- `openFileOutput` con `MODE_APPEND` agrega contenido sin sobrescribir.
- Room requiere **KSP** para generar las implementaciones de DAO y Database en tiempo de compilación.
- La versión de KSP debe coincidir con la versión de Kotlin (`2.2.10-1.0.28` para Kotlin `2.2.10`).

### Recursos adicionales

- [SharedPreferences — Android Developers](https://developer.android.com/training/data-storage/shared-preferences)
- [DataStore — Android Developers](https://developer.android.com/topic/libraries/architecture/datastore)
- [Almacenamiento interno — Android Developers](https://developer.android.com/training/data-storage/app-specific)
- [Room Persistence Library — Android Developers](https://developer.android.com/training/data-storage/room)
- [KSP (Kotlin Symbol Processing) — GitHub](https://github.com/google/ksp)

---
