---LAB_START---
LAB_ID: 04-00-01
---MARKDOWN---
# Laboratorio 4: Construcción de una interfaz interactiva con recursos Android

## Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 288 minutos (6 bloques × ~48 min) |
| **Complejidad** | Alta |
| **Nivel Bloom** | Crear |

## Descripción General

En este laboratorio transformarás la aplicación **TaskManager** del Laboratorio 3 en una aplicación con interfaz de usuario profesional e interactiva llamada **TaskManagerUI**. A lo largo de 6 bloques progresivos, implementarás layouts complejos con `ConstraintLayout`, un `RecyclerView` completo con `Adapter`, `ViewHolder` y `DiffUtil`, formularios con validación en tiempo real, gestión organizada de recursos Android, View Binding y navegación integrada entre pantallas. Al finalizar, tendrás una aplicación funcional de gestión de tareas con una interfaz pulida y código bien estructurado.

## Objetivos de Aprendizaje

- [ ] Diseñar layouts complejos usando `ConstraintLayout` como contenedor principal, combinándolo con `LinearLayout` y `FrameLayout` para organizar la interfaz de usuario
- [ ] Implementar y personalizar un `RecyclerView` completo con `Adapter`, `ViewHolder`, `LayoutManager` y `DiffUtil.ItemCallback`
- [ ] Manejar eventos de usuario mediante clics en botones, `TextWatcher` para validación en tiempo real y selecciones en `Spinner`
- [ ] Organizar y gestionar recursos Android: `strings.xml`, `colors.xml`, `dimens.xml`, `themes.xml` y recursos `drawable`
- [ ] Aplicar View Binding y buenas prácticas de diseño, integrando navegación entre pantallas

## Prerrequisitos

### Conocimiento Previo

| Requisito | Fuente |
|---|---|
| Laboratorio 3 completado: aplicación TaskManager con 4 Activities, navegación con Intents y ViewModel con LiveData | Laboratorio 3 |
| Ciclo de vida de Activities, Intents explícitos y extras | Laboratorio 3 |
| Modelo de datos `Tarea` y repositorio en memoria | Laboratorio 2 |
| Fundamentos de Kotlin: clases de datos, lambdas, colecciones | Laboratorio 2 |

### Acceso y Herramientas

| Herramienta | Versión |
|---|---|
| Android Studio | Quail 3 (2026.1.3 Patch 1) |
| Kotlin | 2.2.10 |
| Android Gradle Plugin | 9.3.2 |
| Gradle | 9.3.2 |
| JDK | 11 |
| AVDs configurados | API 30, 35, 36, 37 |

## Entorno del Laboratorio

### Estructura del Proyecto

```
C:\AndroidCursoBasico\Lab4\TaskManagerUI\    (Windows)
~/AndroidCursoBasico/Lab4/TaskManagerUI/     (macOS/Linux)
```

### Configuración SDK

| Parámetro | Valor |
|---|---|
| `compileSdk` | 37 |
| `minSdk` | 30 |
| `targetSdk` | 37 |
| `jvmTarget` | `"11"` |
| `sourceCompatibility` | `JavaVersion.VERSION_11` |
| `targetCompatibility` | `JavaVersion.VERSION_11` |

---

## Paso 1 — Crear el Proyecto TaskManagerUI y Configurar Gradle

**Objetivo:** Crear el proyecto base con la plantilla correcta y configurar todas las dependencias necesarias, incluyendo RecyclerView.

### Instrucciones

1. Abre **Android Studio Quail 3** y selecciona **File → New → New Project**.

2. Selecciona la plantilla **Empty Views Activity** (no "Empty Activity", que es para Compose).

3. Configura el proyecto con estos valores:

   | Campo | Valor |
   |---|---|
   | **Name** | TaskManagerUI |
   | **Package name** | `com.cursokotlin.android.taskmanagerui` |
   | **Save location** | `C:\AndroidCursoBasico\Lab4\TaskManagerUI` (Windows) o `~/AndroidCursoBasico/Lab4/TaskManagerUI` (macOS/Linux) |
   | **Language** | Kotlin |
   | **Minimum SDK** | API 30: Android 11 (R) |
   | **Build configuration language** | Kotlin DSL (build.gradle.kts) |

4. Haz clic en **Finish** y espera a que Gradle sincronice.

5. Abre el archivo `gradle/libs.versions.toml` y reemplaza su contenido completo con:

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

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-lifecycle-viewmodel-ktx = { group = "androidx.lifecycle", name = "lifecycle-viewmodel-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-lifecycle-livedata-ktx = { group = "androidx.lifecycle", name = "lifecycle-livedata-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
androidx-recyclerview = { group = "androidx.recyclerview", name = "recyclerview", version.ref = "recyclerview" }
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitExt" }
androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

6. Abre `app/build.gradle.kts` y reemplaza su contenido con:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "com.cursokotlin.android.taskmanagerui"
    compileSdk = 37

    defaultConfig {
        applicationId = "com.cursokotlin.android.taskmanagerui"
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
    implementation(libs.androidx.lifecycle.viewmodel.ktx)
    implementation(libs.androidx.lifecycle.livedata.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.material)
    implementation(libs.androidx.constraintlayout)
    implementation(libs.androidx.recyclerview)

    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
}
```

7. Abre `build.gradle.kts` (nivel raíz del proyecto) y verifica que contenga:

```kotlin
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
}
```

8. Sincroniza Gradle: **File → Sync Project with Gradle Files** o haz clic en el ícono de elefante con la flecha azul.

### Resultado Esperado

La sincronización de Gradle debe completarse sin errores. En la ventana **Build** deberías ver:

```
BUILD SUCCESSFUL
```

### Verificación

- [ ] El proyecto compila sin errores
- [ ] `viewBinding = true` está habilitado en `buildFeatures`
- [ ] La dependencia `androidx-recyclerview` aparece en `libs.versions.toml`
- [ ] `compileSdk = 37`, `minSdk = 30`, `targetSdk = 37` están configurados

---

## Paso 2 — Crear el Modelo de Datos y el Repositorio

**Objetivo:** Establecer el modelo de datos `Tarea` y el repositorio en memoria que servirán como fuente de datos para toda la aplicación.

### Instrucciones

1. Crea el paquete `model` dentro de `com.cursokotlin.android.taskmanagerui`: haz clic derecho en el paquete principal → **New → Package** → escribe `model`.

2. Crea el paquete `repository` de la misma forma.

3. Dentro del paquete `model`, crea el archivo `Tarea.kt`:

```kotlin
package com.cursokotlin.android.taskmanagerui.model

data class Tarea(
    val id: Int,
    var titulo: String,
    var descripcion: String,
    var estado: EstadoTarea = EstadoTarea.PENDIENTE
)

enum class EstadoTarea {
    PENDIENTE,
    EN_PROGRESO,
    COMPLETADA
}
```

4. Dentro del paquete `repository`, crea el archivo `TareaRepository.kt`:

```kotlin
package com.cursokotlin.android.taskmanagerui.repository

import com.cursokotlin.android.taskmanagerui.model.EstadoTarea
import com.cursokotlin.android.taskmanagerui.model.Tarea

object TareaRepository {

    private val tareas = mutableListOf<Tarea>()
    private var siguienteId = 1

    init {
        // Datos de ejemplo para desarrollo
        agregarTarea("Configurar proyecto", "Crear TaskManagerUI con Gradle configurado")
        agregarTarea("Diseñar layouts", "Implementar ConstraintLayout principal")
        agregarTarea("Implementar RecyclerView", "Crear Adapter y ViewHolder")
        agregarTarea("Agregar recursos", "Definir strings, colors y dimens")
        agregarTarea("Pruebas en emulador", "Verificar en API 30 y 37")
    }

    fun obtenerTodas(): List<Tarea> = tareas.toList()

    fun obtenerPorId(id: Int): Tarea? = tareas.find { it.id == id }

    fun agregarTarea(titulo: String, descripcion: String): Tarea {
        val tarea = Tarea(
            id = siguienteId++,
            titulo = titulo,
            descripcion = descripcion
        )
        tareas.add(tarea)
        return tarea
    }

    fun actualizarEstado(id: Int, nuevoEstado: EstadoTarea): Boolean {
        val tarea = tareas.find { it.id == id } ?: return false
        tarea.estado = nuevoEstado
        return true
    }

    fun eliminarTarea(id: Int): Boolean {
        return tareas.removeAll { it.id == id }
    }

    fun filtrarPorEstado(estado: EstadoTarea): List<Tarea> {
        return tareas.filter { it.estado == estado }
    }
}
```

### Resultado Esperado

La estructura del paquete debe verse así:

```
com.cursokotlin.android.taskmanagerui/
├── model/
│   └── Tarea.kt
├── repository/
│   └── TareaRepository.kt
└── MainActivity.kt
```

### Verificación

- [ ] El proyecto compila sin errores después de agregar ambos archivos
- [ ] `TareaRepository` es un `object` (singleton) con datos de ejemplo inicializados
- [ ] `EstadoTarea` tiene tres valores: `PENDIENTE`, `EN_PROGRESO`, `COMPLETADA`

---

## Paso 3 — Bloque 1: Diseñar el Layout Principal con ConstraintLayout

**Objetivo:** Rediseñar la pantalla principal usando `ConstraintLayout` como contenedor raíz, combinándolo con `LinearLayout` horizontal para una barra de filtros y un `FrameLayout` para el área de contenido.

### Instrucciones

1. Abre `res/values/strings.xml` y reemplaza su contenido con los textos necesarios para este paso (se completarán más en el Bloque 4):

```xml
<resources>
    <string name="app_name">TaskManagerUI</string>
    <string name="titulo_mis_tareas">Mis Tareas</string>
    <string name="filtro_todas">Todas</string>
    <string name="filtro_pendientes">Pendientes</string>
    <string name="filtro_en_progreso">En Progreso</string>
    <string name="filtro_completadas">Completadas</string>
    <string name="btn_nueva_tarea">Nueva Tarea</string>
    <string name="sin_tareas">No hay tareas para mostrar</string>
    <string name="titulo_crear_tarea">Crear Tarea</string>
    <string name="hint_titulo_tarea">Título de la tarea</string>
    <string name="hint_descripcion_tarea">Descripción de la tarea</string>
    <string name="btn_guardar">Guardar</string>
    <string name="titulo_detalles">Detalles de Tarea</string>
    <string name="label_estado">Estado:</string>
    <string name="label_descripcion">Descripción:</string>
    <string name="error_titulo_vacio">El título no puede estar vacío</string>
    <string name="tarea_creada">Tarea creada exitosamente</string>
    <string name="estado_pendiente">Pendiente</string>
    <string name="estado_en_progreso">En Progreso</string>
    <string name="estado_completada">Completada</string>
</resources>
```

2. Abre `res/layout/activity_main.xml` y reemplaza todo su contenido con el siguiente layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <!-- Título de la pantalla, anclado al borde superior -->
    <TextView
        android:id="@+id/tvTituloPantalla"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="@string/titulo_mis_tareas"
        android:textSize="28sp"
        android:textStyle="bold"
        android:padding="16dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <!-- Barra de filtros horizontal usando LinearLayout -->
    <LinearLayout
        android:id="@+id/llFiltros"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingStart="8dp"
        android:paddingEnd="8dp"
        android:paddingBottom="8dp"
        app:layout_constraintTop_toBottomOf="@id/tvTituloPantalla"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <Button
            android:id="@+id/btnFiltroTodas"
            style="?attr/materialButtonOutlinedStyle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginHorizontal="4dp"
            android:text="@string/filtro_todas"
            android:textSize="12sp" />

        <Button
            android:id="@+id/btnFiltroPendientes"
            style="?attr/materialButtonOutlinedStyle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginHorizontal="4dp"
            android:text="@string/filtro_pendientes"
            android:textSize="12sp" />

        <Button
            android:id="@+id/btnFiltroEnProgreso"
            style="?attr/materialButtonOutlinedStyle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginHorizontal="4dp"
            android:text="@string/filtro_en_progreso"
            android:textSize="12sp" />

        <Button
            android:id="@+id/btnFiltroCompletadas"
            style="?attr/materialButtonOutlinedStyle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginHorizontal="4dp"
            android:text="@string/filtro_completadas"
            android:textSize="12sp" />

    </LinearLayout>

    <!-- Área de contenido usando FrameLayout para superposición -->
    <FrameLayout
        android:id="@+id/flContenido"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toBottomOf="@id/llFiltros"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <!-- RecyclerView para la lista de tareas -->
        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/rvTareas"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:clipToPadding="false"
            android:padding="8dp" />

        <!-- Mensaje cuando no hay tareas (capa superior) -->
        <TextView
            android:id="@+id/tvSinTareas"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:text="@string/sin_tareas"
            android:textSize="16sp"
            android:visibility="gone" />

    </FrameLayout>

    <!-- FloatingActionButton para crear nueva tarea -->
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fabNuevaTarea"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:contentDescription="@string/btn_nueva_tarea"
        android:src="@android:drawable/ic_input_add"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

3. Observa la estructura del layout en el panel **Design** de Android Studio. Verifica que:
   - El `TextView` del título está anclado al borde superior del padre.
   - El `LinearLayout` de filtros está debajo del título (`constraintTop_toBottomOf="@id/tvTituloPantalla"`).
   - El `FrameLayout` ocupa todo el espacio restante entre los filtros y el borde inferior.
   - El `FloatingActionButton` está anclado a la esquina inferior derecha.

4. Cambia al modo **Blueprint** en el editor de layouts para ver las restricciones representadas como flechas. Verifica que todas las vistas tienen restricciones completas (horizontal y vertical).

### Resultado Esperado

En el panel de vista previa, deberías ver:
- Un título "Mis Tareas" en la parte superior
- Una fila de 4 botones de filtro distribuidos equitativamente
- Un área central vacía (donde irá el RecyclerView)
- Un botón flotante circular en la esquina inferior derecha

### Verificación

- [ ] El layout raíz es `ConstraintLayout`
- [ ] El `LinearLayout` usa `orientation="horizontal"` con `layout_weight="1"` en cada botón
- [ ] El `FrameLayout` contiene el `RecyclerView` y el `TextView` de "sin tareas" superpuestos
- [ ] Todas las vistas tienen restricciones horizontales y verticales completas
- [ ] No aparecen advertencias de "missing constraints" en el editor

---

## Paso 4 — Bloque 2: Implementar RecyclerView con Adapter, ViewHolder y DiffUtil

**Objetivo:** Crear la infraestructura completa del `RecyclerView` para mostrar la lista de tareas con actualizaciones eficientes.

### Instrucciones

1. Crea el directorio de layout para el ítem: haz clic derecho en `res/layout/` → **New → Layout Resource File**. Nómbralo `item_tarea.xml` y configúralo con `ConstraintLayout` como elemento raíz.

2. Reemplaza el contenido de `res/layout/item_tarea.xml` con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginHorizontal="8dp"
    android:layout_marginVertical="4dp"
    app:cardElevation="2dp"
    app:cardCornerRadius="8dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <!-- Ícono indicador de estado -->
        <ImageView
            android:id="@+id/ivEstadoIcono"
            android:layout_width="24dp"
            android:layout_height="24dp"
            android:contentDescription="@string/label_estado"
            android:src="@android:drawable/presence_online"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent" />

        <!-- Título de la tarea -->
        <TextView
            android:id="@+id/tvTituloTarea"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="12dp"
            android:layout_marginEnd="8dp"
            android:textSize="16sp"
            android:textStyle="bold"
            android:maxLines="1"
            android:ellipsize="end"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toEndOf="@id/ivEstadoIcono"
            app:layout_constraintEnd_toStartOf="@id/tvEstadoTarea" />

        <!-- Descripción de la tarea -->
        <TextView
            android:id="@+id/tvDescripcionTarea"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="12dp"
            android:layout_marginTop="4dp"
            android:layout_marginEnd="8dp"
            android:textSize="14sp"
            android:maxLines="2"
            android:ellipsize="end"
            app:layout_constraintTop_toBottomOf="@id/tvTituloTarea"
            app:layout_constraintStart_toEndOf="@id/ivEstadoIcono"
            app:layout_constraintEnd_toEndOf="parent" />

        <!-- Etiqueta de estado -->
        <TextView
            android:id="@+id/tvEstadoTarea"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="12sp"
            android:paddingHorizontal="8dp"
            android:paddingVertical="2dp"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</com.google.android.material.card.MaterialCardView>
```

3. Crea el paquete `adapter` dentro del paquete principal.

4. Dentro del paquete `adapter`, crea el archivo `TareaAdapter.kt`:

```kotlin
package com.cursokotlin.android.taskmanagerui.adapter

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.DiffUtil
import androidx.recyclerview.widget.ListAdapter
import androidx.recyclerview.widget.RecyclerView
import com.cursokotlin.android.taskmanagerui.R
import com.cursokotlin.android.taskmanagerui.databinding.ItemTareaBinding
import com.cursokotlin.android.taskmanagerui.model.EstadoTarea
import com.cursokotlin.android.taskmanagerui.model.Tarea

class TareaAdapter(
    private val onTareaClick: (Tarea) -> Unit
) : ListAdapter<Tarea, TareaAdapter.TareaViewHolder>(TareaDiffCallback()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TareaViewHolder {
        val binding = ItemTareaBinding.inflate(
            LayoutInflater.from(parent.context),
            parent,
            false
        )
        return TareaViewHolder(binding)
    }

    override fun onBindViewHolder(holder: TareaViewHolder, position: Int) {
        holder.bind(getItem(position))
    }

    inner class TareaViewHolder(
        private val binding: ItemTareaBinding
    ) : RecyclerView.ViewHolder(binding.root) {

        init {
            binding.root.setOnClickListener {
                val position = adapterPosition
                if (position != RecyclerView.NO_POSITION) {
                    onTareaClick(getItem(position))
                }
            }
        }

        fun bind(tarea: Tarea) {
            binding.tvTituloTarea.text = tarea.titulo
            binding.tvDescripcionTarea.text = tarea.descripcion

            val context = binding.root.context

            when (tarea.estado) {
                EstadoTarea.PENDIENTE -> {
                    binding.tvEstadoTarea.text = context.getString(R.string.estado_pendiente)
                    binding.tvEstadoTarea.setBackgroundResource(
                        android.R.color.holo_orange_light
                    )
                    binding.ivEstadoIcono.setImageResource(
                        android.R.drawable.presence_away
                    )
                }
                EstadoTarea.EN_PROGRESO -> {
                    binding.tvEstadoTarea.text = context.getString(R.string.estado_en_progreso)
                    binding.tvEstadoTarea.setBackgroundResource(
                        android.R.color.holo_blue_light
                    )
                    binding.ivEstadoIcono.setImageResource(
                        android.R.drawable.presence_online
                    )
                }
                EstadoTarea.COMPLETADA -> {
                    binding.tvEstadoTarea.text = context.getString(R.string.estado_completada)
                    binding.tvEstadoTarea.setBackgroundResource(
                        android.R.color.holo_green_light
                    )
                    binding.ivEstadoIcono.setImageResource(
                        android.R.drawable.presence_online
                    )
                }
            }
        }
    }

    class TareaDiffCallback : DiffUtil.ItemCallback<Tarea>() {
        override fun areItemsTheSame(oldItem: Tarea, newItem: Tarea): Boolean {
            return oldItem.id == newItem.id
        }

        override fun areContentsTheSame(oldItem: Tarea, newItem: Tarea): Boolean {
            return oldItem == newItem
        }
    }
}
```

5. Crea el paquete `viewmodel` y dentro crea `TareaViewModel.kt`:

```kotlin
package com.cursokotlin.android.taskmanagerui.viewmodel

import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel
import com.cursokotlin.android.taskmanagerui.model.EstadoTarea
import com.cursokotlin.android.taskmanagerui.model.Tarea
import com.cursokotlin.android.taskmanagerui.repository.TareaRepository

class TareaViewModel : ViewModel() {

    private val _tareas = MutableLiveData<List<Tarea>>()
    val tareas: LiveData<List<Tarea>> get() = _tareas

    private val _filtroActual = MutableLiveData<EstadoTarea?>()
    val filtroActual: LiveData<EstadoTarea?> get() = _filtroActual

    init {
        cargarTareas()
    }

    fun cargarTareas() {
        val filtro = _filtroActual.value
        _tareas.value = if (filtro == null) {
            TareaRepository.obtenerTodas()
        } else {
            TareaRepository.filtrarPorEstado(filtro)
        }
    }

    fun aplicarFiltro(estado: EstadoTarea?) {
        _filtroActual.value = estado
        cargarTareas()
    }

    fun agregarTarea(titulo: String, descripcion: String) {
        TareaRepository.agregarTarea(titulo, descripcion)
        cargarTareas()
    }

    fun obtenerTareaPorId(id: Int): Tarea? {
        return TareaRepository.obtenerPorId(id)
    }
}
```

6. Ahora conecta todo en `MainActivity.kt`. Reemplaza su contenido:

```kotlin
package com.cursokotlin.android.taskmanagerui

import android.content.Intent
import android.os.Bundle
import android.view.View
import androidx.activity.viewModels
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import com.cursokotlin.android.taskmanagerui.adapter.TareaAdapter
import com.cursokotlin.android.taskmanagerui.databinding.ActivityMainBinding
import com.cursokotlin.android.taskmanagerui.model.EstadoTarea
import com.cursokotlin.android.taskmanagerui.viewmodel.TareaViewModel

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private val viewModel: TareaViewModel by viewModels()
    private lateinit var tareaAdapter: TareaAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        configurarRecyclerView()
        configurarFiltros()
        configurarFab()
        observarDatos()
    }

    override fun onResume() {
        super.onResume()
        // Recargar tareas al volver de otra pantalla
        viewModel.cargarTareas()
    }

    private fun configurarRecyclerView() {
        tareaAdapter = TareaAdapter { tarea ->
            val intent = Intent(this, DetallesTareaActivity::class.java)
            intent.putExtra("TAREA_ID", tarea.id)
            startActivity(intent)
        }

        binding.rvTareas.apply {
            layoutManager = LinearLayoutManager(this@MainActivity)
            adapter = tareaAdapter
        }
    }

    private fun configurarFiltros() {
        binding.btnFiltroTodas.setOnClickListener {
            viewModel.aplicarFiltro(null)
        }
        binding.btnFiltroPendientes.setOnClickListener {
            viewModel.aplicarFiltro(EstadoTarea.PENDIENTE)
        }
        binding.btnFiltroEnProgreso.setOnClickListener {
            viewModel.aplicarFiltro(EstadoTarea.EN_PROGRESO)
        }
        binding.btnFiltroCompletadas.setOnClickListener {
            viewModel.aplicarFiltro(EstadoTarea.COMPLETADA)
        }
    }

    private fun configurarFab() {
        binding.fabNuevaTarea.setOnClickListener {
            val intent = Intent(this, CrearTareaActivity::class.java)
            startActivity(intent)
        }
    }

    private fun observarDatos() {
        viewModel.tareas.observe(this) { listaTareas ->
            tareaAdapter.submitList(listaTareas)

            if (listaTareas.isEmpty()) {
                binding.tvSinTareas.visibility = View.VISIBLE
                binding.rvTareas.visibility = View.GONE
            } else {
                binding.tvSinTareas.visibility = View.GONE
                binding.rvTareas.visibility = View.VISIBLE
            }
        }
    }
}
```

> **Nota:** El proyecto aún no compilará porque faltan `DetallesTareaActivity` y `CrearTareaActivity`. Las crearemos en los pasos siguientes.

### Resultado Esperado

La estructura del proyecto ahora incluye:

```
com.cursokotlin.android.taskmanagerui/
├── adapter/
│   └── TareaAdapter.kt
├── model/
│   └── Tarea.kt
├── repository/
│   └── TareaRepository.kt
├── viewmodel/
│   └── TareaViewModel.kt
└── MainActivity.kt
```

### Verificación

- [ ] `TareaAdapter` extiende `ListAdapter` con `TareaDiffCallback`
- [ ] `TareaViewHolder` usa View Binding (`ItemTareaBinding`)
- [ ] `DiffUtil.ItemCallback` compara por `id` en `areItemsTheSame` y por igualdad completa en `areContentsTheSame`
- [ ] `TareaViewModel` expone `LiveData<List<Tarea>>` y soporta filtrado por estado
- [ ] `MainActivity` configura `LinearLayoutManager` y observa el `LiveData`

---

## Paso 5 — Bloque 3: Formulario de Creación con Widgets y Eventos

**Objetivo:** Implementar la pantalla de creación de tareas con `EditText`, `Spinner`, `Button`, validación en tiempo real con `TextWatcher` y el patrón de View Binding.

### Instrucciones

1. Crea el archivo de layout `res/layout/activity_crear_tarea.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".CrearTareaActivity">

    <!-- Título de la pantalla -->
    <TextView
        android:id="@+id/tvTituloCrear"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="@string/titulo_crear_tarea"
        android:textSize="24sp"
        android:textStyle="bold"
        android:layout_marginBottom="24dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <!-- Campo de título de la tarea -->
    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/tilTitulo"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:hint="@string/hint_titulo_tarea"
        app:layout_constraintTop_toBottomOf="@id/tvTituloCrear"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etTituloTarea"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="text"
            android:maxLines="1" />

    </com.google.android.material.textfield.TextInputLayout>

    <!-- Campo de descripción de la tarea -->
    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/tilDescripcion"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:hint="@string/hint_descripcion_tarea"
        app:layout_constraintTop_toBottomOf="@id/tilTitulo"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etDescripcionTarea"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="textMultiLine"
            android:minLines="3"
            android:gravity="top" />

    </com.google.android.material.textfield.TextInputLayout>

    <!-- Selector de estado -->
    <TextView
        android:id="@+id/tvLabelEstado"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/label_estado"
        android:textSize="16sp"
        android:layout_marginTop="16dp"
        app:layout_constraintTop_toBottomOf="@id/tilDescripcion"
        app:layout_constraintStart_toStartOf="parent" />

    <Spinner
        android:id="@+id/spEstado"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="12dp"
        android:layout_marginTop="16dp"
        app:layout_constraintTop_toBottomOf="@id/tilDescripcion"
        app:layout_constraintStart_toEndOf="@id/tvLabelEstado"
        app:layout_constraintEnd_toEndOf="parent" />

    <!-- Botón de guardar -->
    <Button
        android:id="@+id/btnGuardar"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="@string/btn_guardar"
        android:enabled="false"
        android:layout_marginTop="32dp"
        app:layout_constraintTop_toBottomOf="@id/spEstado"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

2. Crea la clase `CrearTareaActivity.kt` en el paquete principal:

```kotlin
package com.cursokotlin.android.taskmanagerui

import android.os.Bundle
import android.text.Editable
import android.text.TextWatcher
import android.widget.ArrayAdapter
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanagerui.databinding.ActivityCrearTareaBinding
import com.cursokotlin.android.taskmanagerui.model.EstadoTarea
import com.cursokotlin.android.taskmanagerui.repository.TareaRepository

class CrearTareaActivity : AppCompatActivity() {

    private lateinit var binding: ActivityCrearTareaBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityCrearTareaBinding.inflate(layoutInflater)
        setContentView(binding.root)

        configurarSpinner()
        configurarValidacion()
        configurarBotonGuardar()
        configurarNavegacionRetroceso()
    }

    private fun configurarSpinner() {
        val estados = arrayOf(
            getString(R.string.estado_pendiente),
            getString(R.string.estado_en_progreso),
            getString(R.string.estado_completada)
        )

        val adapter = ArrayAdapter(
            this,
            android.R.layout.simple_spinner_item,
            estados
        )
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item)
        binding.spEstado.adapter = adapter
    }

    private fun configurarValidacion() {
        val textWatcher = object : TextWatcher {
            override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {
                // No se necesita acción antes del cambio
            }

            override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
                // No se necesita acción durante el cambio
            }

            override fun afterTextChanged(s: Editable?) {
                validarFormulario()
            }
        }

        binding.etTituloTarea.addTextChangedListener(textWatcher)
        binding.etDescripcionTarea.addTextChangedListener(textWatcher)
    }

    private fun validarFormulario() {
        val tituloValido = binding.etTituloTarea.text.toString().trim().isNotEmpty()
        val descripcionValida = binding.etDescripcionTarea.text.toString().trim().isNotEmpty()

        binding.btnGuardar.isEnabled = tituloValido && descripcionValida

        // Mostrar error en el campo de título si está vacío y ya fue tocado
        if (!tituloValido && binding.etTituloTarea.hasFocus()) {
            binding.tilTitulo.error = getString(R.string.error_titulo_vacio)
        } else {
            binding.tilTitulo.error = null
        }
    }

    private fun configurarBotonGuardar() {
        binding.btnGuardar.setOnClickListener {
            val titulo = binding.etTituloTarea.text.toString().trim()
            val descripcion = binding.etDescripcionTarea.text.toString().trim()
            val estadoSeleccionado = when (binding.spEstado.selectedItemPosition) {
                0 -> EstadoTarea.PENDIENTE
                1 -> EstadoTarea.EN_PROGRESO
                2 -> EstadoTarea.COMPLETADA
                else -> EstadoTarea.PENDIENTE
            }

            val tarea = TareaRepository.agregarTarea(titulo, descripcion)
            TareaRepository.actualizarEstado(tarea.id, estadoSeleccionado)

            Toast.makeText(this, getString(R.string.tarea_creada), Toast.LENGTH_SHORT).show()
            finish()
        }
    }

    private fun configurarNavegacionRetroceso() {
        supportActionBar?.setDisplayHomeAsUpEnabled(true)
    }

    override fun onSupportNavigateUp(): Boolean {
        finish()
        return true
    }
}
```

3. Registra la nueva Activity en `AndroidManifest.xml`. Abre `app/src/main/AndroidManifest.xml` y agrega dentro de la etiqueta `<application>`, debajo de la `<activity>` existente de `MainActivity`:

```xml
<activity
    android:name=".CrearTareaActivity"
    android:exported="false"
    android:parentActivityName=".MainActivity" />
```

### Resultado Esperado

Al ejecutar la app y tocar el FAB, debería abrirse la pantalla de creación con:
- Un título "Crear Tarea"
- Un campo de texto para el título con hint
- Un campo de texto multilínea para la descripción
- Un Spinner con tres opciones de estado
- Un botón "Guardar" inicialmente deshabilitado (gris)

Al escribir texto en ambos campos, el botón "Guardar" se habilita automáticamente.

### Verificación

- [ ] El `TextWatcher` habilita/deshabilita el botón según el contenido de ambos campos
- [ ] El `Spinner` muestra las tres opciones de estado
- [ ] El `TextInputLayout` muestra un mensaje de error cuando el título está vacío
- [ ] Al guardar, la tarea se agrega al repositorio y la Activity se cierra con `finish()`
- [ ] La flecha de retroceso en la barra de acción funciona correctamente

---

## Paso 6 — Bloque 3 (continuación): Pantalla de Detalles de Tarea

**Objetivo:** Crear la pantalla de detalles que muestra la información completa de una tarea seleccionada desde el RecyclerView.

### Instrucciones

1. Crea el archivo de layout `res/layout/activity_detalles_tarea.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".DetallesTareaActivity">

    <!-- Título de la tarea -->
    <TextView
        android:id="@+id/tvDetalleTitulo"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="24sp"
        android:textStyle="bold"
        android:layout_marginBottom="16dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        tools:text="Título de ejemplo" />

    <!-- Etiqueta de estado -->
    <TextView
        android:id="@+id/tvLabelEstadoDetalle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/label_estado"
        android:textSize="16sp"
        android:textStyle="bold"
        android:layout_marginTop="16dp"
        app:layout_constraintTop_toBottomOf="@id/tvDetalleTitulo"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/tvDetalleEstado"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="16dp"
        android:paddingHorizontal="12dp"
        android:paddingVertical="4dp"
        app:layout_constraintTop_toBottomOf="@id/tvDetalleTitulo"
        app:layout_constraintStart_toEndOf="@id/tvLabelEstadoDetalle"
        tools:text="Pendiente" />

    <!-- Etiqueta de descripción -->
    <TextView
        android:id="@+id/tvLabelDescripcionDetalle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/label_descripcion"
        android:textSize="16sp"
        android:textStyle="bold"
        android:layout_marginTop="24dp"
        app:layout_constraintTop_toBottomOf="@id/tvLabelEstadoDetalle"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/tvDetalleDescripcion"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:layout_marginTop="8dp"
        app:layout_constraintTop_toBottomOf="@id/tvLabelDescripcionDetalle"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        tools:text="Descripción de ejemplo de la tarea" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

2. Crea la clase `DetallesTareaActivity.kt`:

```kotlin
package com.cursokotlin.android.taskmanagerui

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.cursokotlin.android.taskmanagerui.databinding.ActivityDetallesTareaBinding
import com.cursokotlin.android.taskmanagerui.model.EstadoTarea
import com.cursokotlin.android.taskmanagerui.repository.TareaRepository

class DetallesTareaActivity : AppCompatActivity() {

    private lateinit var binding: ActivityDetallesTareaBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityDetallesTareaBinding.inflate(layoutInflater)
        setContentView(binding.root)

        configurarNavegacionRetroceso()
        cargarDatosTarea()
    }

    private fun configurarNavegacionRetroceso() {
        supportActionBar?.setDisplayHomeAsUpEnabled(true)
        supportActionBar?.title = getString(R.string.titulo_detalles)
    }

    private fun cargarDatosTarea() {
        val tareaId = intent.getIntExtra("TAREA_ID", -1)

        if (tareaId == -1) {
            finish()
            return
        }

        val tarea = TareaRepository.obtenerPorId(tareaId)

        if (tarea == null) {
            finish()
            return
        }

        binding.tvDetalleTitulo.text = tarea.titulo
        binding.tvDetalleDescripcion.text = tarea.descripcion

        when (tarea.estado) {
            EstadoTarea.PENDIENTE -> {
                binding.tvDetalleEstado.text = getString(R.string.estado_pendiente)
                binding.tvDetalleEstado.setBackgroundResource(
                    android.R.color.holo_orange_light
                )
            }
            EstadoTarea.EN_PROGRESO -> {
                binding.tvDetalleEstado.text = getString(R.string.estado_en_progreso)
                binding.tvDetalleEstado.setBackgroundResource(
                    android.R.color.holo_blue_light
                )
            }
            EstadoTarea.COMPLETADA -> {
                binding.tvDetalleEstado.text = getString(R.string.estado_completada)
                binding.tvDetalleEstado.setBackgroundResource(
                    android.R.color.holo_green_light
                )
            }
        }
    }

    override fun onSupportNavigateUp(): Boolean {
        finish()
        return true
    }
}
```

3. Registra la Activity en `AndroidManifest.xml`:

```xml
<activity
    android:name=".DetallesTareaActivity"
    android:exported="false"
    android:parentActivityName=".MainActivity" />
```

4. En este punto, compila el proyecto: **Build → Make Project** (Ctrl+F9 / Cmd+F9).

### Resultado Esperado

El proyecto debe compilar exitosamente. El `AndroidManifest.xml` completo dentro de `<application>` debe incluir tres activities:

```
MainActivity (launcher)
CrearTareaActivity
DetallesTareaActivity
```

### Verificación

- [ ] El proyecto compila sin errores
- [ ] `DetallesTareaActivity` recibe el `TAREA_ID` vía Intent extra
- [ ] Si el ID es inválido o la tarea no existe, la Activity se cierra con `finish()`
- [ ] Los colores de fondo del estado cambian según `EstadoTarea`
- [ ] La flecha de retroceso funciona correctamente

---

## Paso 7 — Bloque 4: Organización de Recursos Android

**Objetivo:** Definir una paleta de colores, dimensiones consistentes, personalizar el tema Material 3 y agregar iconos vectoriales.

### Instrucciones

1. Abre `res/values/colors.xml` y reemplaza su contenido:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Colores primarios de la app -->
    <color name="primario">#1565C0</color>
    <color name="primario_oscuro">#0D47A1</color>
    <color name="primario_variante">#42A5F5</color>

    <!-- Colores secundarios -->
    <color name="secundario">#FF6F00</color>
    <color name="secundario_variante">#FFA000</color>

    <!-- Colores de fondo -->
    <color name="fondo">#FAFAFA</color>
    <color name="superficie">#FFFFFF</color>

    <!-- Colores de texto -->
    <color name="texto_primario">#212121</color>
    <color name="texto_secundario">#757575</color>

    <!-- Colores de estado de tareas -->
    <color name="estado_pendiente">#FFF3E0</color>
    <color name="estado_pendiente_texto">#E65100</color>
    <color name="estado_en_progreso">#E3F2FD</color>
    <color name="estado_en_progreso_texto">#0D47A1</color>
    <color name="estado_completada">#E8F5E9</color>
    <color name="estado_completada_texto">#1B5E20</color>

    <!-- Color de error -->
    <color name="error">#B00020</color>

    <!-- Colores base requeridos -->
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
</resources>
```

2. Crea el archivo `res/values/dimens.xml` (clic derecho en `res/values/` → **New → Values Resource File** → nombre: `dimens`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Márgenes -->
    <dimen name="margen_pequeno">4dp</dimen>
    <dimen name="margen_normal">8dp</dimen>
    <dimen name="margen_medio">12dp</dimen>
    <dimen name="margen_grande">16dp</dimen>
    <dimen name="margen_extra_grande">24dp</dimen>

    <!-- Paddings -->
    <dimen name="padding_pequeno">4dp</dimen>
    <dimen name="padding_normal">8dp</dimen>
    <dimen name="padding_grande">16dp</dimen>

    <!-- Tamaños de texto -->
    <dimen name="texto_pequeno">12sp</dimen>
    <dimen name="texto_normal">14sp</dimen>
    <dimen name="texto_medio">16sp</dimen>
    <dimen name="texto_grande">20sp</dimen>
    <dimen name="texto_titulo">24sp</dimen>
    <dimen name="texto_titulo_grande">28sp</dimen>

    <!-- Dimensiones de componentes -->
    <dimen name="elevacion_tarjeta">2dp</dimen>
    <dimen name="radio_tarjeta">8dp</dimen>
    <dimen name="tamano_icono_estado">24dp</dimen>
</resources>
```

3. Abre `res/values/themes.xml` y actualiza el tema para usar los colores personalizados:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="Theme.TaskManagerUI" parent="Theme.Material3.Light.NoActionBar">
        <!-- Colores primarios -->
        <item name="colorPrimary">@color/primario</item>
        <item name="colorPrimaryVariant">@color/primario_oscuro</item>
        <item name="colorOnPrimary">@color/white</item>

        <!-- Colores secundarios -->
        <item name="colorSecondary">@color/secundario</item>
        <item name="colorSecondaryVariant">@color/secundario_variante</item>
        <item name="colorOnSecondary">@color/black</item>

        <!-- Colores de fondo -->
        <item name="android:colorBackground">@color/fondo</item>
        <item name="colorSurface">@color/superficie</item>

        <!-- Color de error -->
        <item name="colorError">@color/error</item>
    </style>
</resources>
```

> **Nota:** Si usas `Theme.Material3.Light.NoActionBar`, no tendrás ActionBar por defecto. Esto es intencional; el título ya está en el layout. Si prefieres conservar la ActionBar, usa `Theme.Material3.Light` en su lugar y ajusta `configurarNavegacionRetroceso()` en las Activities secundarias.

4. Verifica que el `AndroidManifest.xml` usa el tema correcto. Busca `android:theme` en la etiqueta `<application>`:

```xml
android:theme="@style/Theme.TaskManagerUI"
```

5. Agrega un ícono vectorial para el FAB. Ve a **File → New → Vector Asset**:
   - **Asset type:** Clip Art
   - **Clip Art:** busca "add" y selecciona el ícono de "+"
   - **Name:** `ic_add`
   - **Size:** 24dp × 24dp
   - **Color:** `#FFFFFF`
   - Haz clic en **Next → Finish**

6. Actualiza el FAB en `activity_main.xml` para usar el nuevo ícono:

```xml
android:src="@drawable/ic_add"
```

7. Ahora actualiza el `TareaAdapter.kt` para usar los colores personalizados del recurso en lugar de los colores del sistema. Modifica el método `bind()` en `TareaViewHolder`:

```kotlin
fun bind(tarea: Tarea) {
    binding.tvTituloTarea.text = tarea.titulo
    binding.tvDescripcionTarea.text = tarea.descripcion

    val context = binding.root.context

    when (tarea.estado) {
        EstadoTarea.PENDIENTE -> {
            binding.tvEstadoTarea.text = context.getString(R.string.estado_pendiente)
            binding.tvEstadoTarea.setBackgroundColor(
                context.getColor(R.color.estado_pendiente)
            )
            binding.tvEstadoTarea.setTextColor(
                context.getColor(R.color.estado_pendiente_texto)
            )
        }
        EstadoTarea.EN_PROGRESO -> {
            binding.tvEstadoTarea.text = context.getString(R.string.estado_en_progreso)
            binding.tvEstadoTarea.setBackgroundColor(
                context.getColor(R.color.estado_en_progreso)
            )
            binding.tvEstadoTarea.setTextColor(
                context.getColor(R.color.estado_en_progreso_texto)
            )
        }
        EstadoTarea.COMPLETADA -> {
            binding.tvEstadoTarea.text = context.getString(R.string.estado_completada)
            binding.tvEstadoTarea.setBackgroundColor(
                context.getColor(R.color.estado_completada)
            )
            binding.tvEstadoTarea.setTextColor(
                context.getColor(R.color.estado_completada_texto)
            )
        }
    }
}
```

8. De forma similar, actualiza `DetallesTareaActivity.kt` para usar los colores personalizados:

```kotlin
when (tarea.estado) {
    EstadoTarea.PENDIENTE -> {
        binding.tvDetalleEstado.text = getString(R.string.estado_pendiente)
        binding.tvDetalleEstado.setBackgroundColor(getColor(R.color.estado_pendiente))
        binding.tvDetalleEstado.setTextColor(getColor(R.color.estado_pendiente_texto))
    }
    EstadoTarea.EN_PROGRESO -> {
        binding.tvDetalleEstado.text = getString(R.string.estado_en_progreso)
        binding.tvDetalleEstado.setBackgroundColor(getColor(R.color.estado_en_progreso))
        binding.tvDetalleEstado.setTextColor(getColor(R.color.estado_en_progreso_texto))
    }
    EstadoTarea.COMPLETADA -> {
        binding.tvDetalleEstado.text = getString(R.string.estado_completada)
        binding.tvDetalleEstado.setBackgroundColor(getColor(R.color.estado_completada))
        binding.tvDetalleEstado.setTextColor(getColor(R.color.estado_completada_texto))
    }
}
```

9. Actualiza los layouts XML para usar las dimensiones de `dimens.xml`. Por ejemplo, en `activity_main.xml`, cambia los valores hardcodeados:

```xml
<!-- Antes -->
android:textSize="28sp"
android:padding="16dp"

<!-- Después -->
android:textSize="@dimen/texto_titulo_grande"
android:padding="@dimen/padding_grande"
```

Aplica el mismo patrón en `item_tarea.xml`:

```xml
<!-- Antes -->
android:padding="16dp"
android:textSize="16sp"

<!-- Después -->
android:padding="@dimen/padding_grande"
android:textSize="@dimen/texto_medio"
```

> **Consejo:** No es necesario cambiar absolutamente todos los valores de una vez. Enfócate en los más repetidos: paddings de 16dp, textSize de 16sp, márgenes de 8dp y 12dp.

### Resultado Esperado

Al ejecutar la app, deberías notar:
- Los colores del tema Material 3 aplicados (azul primario, naranja secundario)
- Las etiquetas de estado con colores de fondo personalizados (naranja para pendiente, azul para en progreso, verde para completada)
- El ícono vectorial `ic_add` en el FAB

### Verificación

- [ ] `colors.xml` contiene al menos 12 colores definidos (primarios, secundarios, estados, fondo, error)
- [ ] `dimens.xml` contiene dimensiones para márgenes, paddings y tamaños de texto
- [ ] `themes.xml` extiende `Theme.Material3.Light.NoActionBar` con colores personalizados
- [ ] El ícono vectorial `ic_add.xml` existe en `res/drawable/`
- [ ] Los textos hardcodeados en layouts se referencian desde `strings.xml`

---

## Paso 8 — Bloque 5: Refactorización con View Binding y Buenas Prácticas

**Objetivo:** Verificar que View Binding está correctamente implementado en todas las Activities, aplicar buenas prácticas de separación de responsabilidades y asegurar que no se bloquea el hilo principal.

### Instrucciones

1. Verifica que `viewBinding = true` está habilitado en `app/build.gradle.kts` (ya lo configuramos en el Paso 1):

```kotlin
buildFeatures {
    viewBinding = true
}
```

2. Revisa que **todas** las Activities usan View Binding correctamente. Verifica el patrón en cada una:

**Patrón correcto de View Binding en una Activity:**

```kotlin
class MiActivity : AppCompatActivity() {

    // 1. Declarar la variable binding como lateinit
    private lateinit var binding: ActivityMiBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 2. Inflar el binding
        binding = ActivityMiBinding.inflate(layoutInflater)

        // 3. Usar binding.root como content view
        setContentView(binding.root)

        // 4. Acceder a vistas mediante binding
        binding.tvTexto.text = "Hola"
    }
}
```

3. Verifica que **ninguna** Activity usa `findViewById()`. Haz una búsqueda global: **Edit → Find → Find in Files** (Ctrl+Shift+F / Cmd+Shift+F), busca `findViewById`. No debe haber resultados en tus archivos Kotlin.

4. Revisa que el `TareaAdapter` también usa View Binding en el `ViewHolder` (ya implementado en el Paso 4 con `ItemTareaBinding`).

5. Verifica la separación de responsabilidades. Tu estructura actual debe seguir este patrón:

| Componente | Responsabilidad |
|---|---|
| `Tarea` (model) | Representar los datos |
| `TareaRepository` (repository) | Gestionar el almacenamiento de datos |
| `TareaViewModel` (viewmodel) | Lógica de presentación y estado |
| `TareaAdapter` (adapter) | Vincular datos con vistas del RecyclerView |
| `MainActivity` | Orquestar UI, observar ViewModel, configurar RecyclerView |
| `CrearTareaActivity` | Formulario de creación con validación |
| `DetallesTareaActivity` | Visualización de detalles de una tarea |

6. Asegúrate de que no hay operaciones costosas en el hilo principal. Revisa que:
   - El repositorio usa operaciones en memoria (listas), que son rápidas
   - No hay llamadas de red ni acceso a disco en `onCreate()` o en los callbacks del adapter
   - Las actualizaciones de la lista usan `submitList()` de `ListAdapter`, que calcula diffs en un hilo de fondo automáticamente

7. Compila y ejecuta la app: **Run → Run 'app'** (Shift+F10 / Ctrl+R).

### Resultado Esperado

La aplicación debe ejecutarse sin errores. Al abrir Logcat (filtrar por el paquete de la app), no deberías ver:
- Errores de `NullPointerException` relacionados con vistas
- Advertencias de `StrictMode` por operaciones en el hilo principal
- Errores de `ClassCastException` en vistas

### Verificación

- [ ] Búsqueda global de `findViewById` no arroja resultados en archivos `.kt`
- [ ] Las tres Activities (`MainActivity`, `CrearTareaActivity`, `DetallesTareaActivity`) usan View Binding
- [ ] `TareaViewHolder` usa `ItemTareaBinding` en lugar de `itemView.findViewById()`
- [ ] No hay operaciones de I/O ni de red en el hilo principal
- [ ] La app compila y ejecuta sin crashes

---

## Paso 9 — Bloque 6: Navegación Integrada entre Pantallas

**Objetivo:** Verificar y probar la navegación completa entre las tres pantallas de la aplicación, asegurando que los datos fluyen correctamente.

### Instrucciones

1. Ejecuta la aplicación en un AVD con **API 30** (el mínimo soportado).

2. **Prueba 1 — Lista principal:** Verifica que la pantalla principal muestra las 5 tareas de ejemplo del repositorio en el RecyclerView.

3. **Prueba 2 — Filtros:** Toca cada botón de filtro:
   - **Todas**: muestra las 5 tareas
   - **Pendientes**: muestra solo tareas con estado `PENDIENTE` (las 5 iniciales)
   - **En Progreso**: muestra lista vacía con mensaje "No hay tareas para mostrar"
   - **Completadas**: muestra lista vacía con mensaje "No hay tareas para mostrar"

4. **Prueba 3 — Navegación a detalles:** Toca cualquier tarea en la lista. Verifica que:
   - Se abre `DetallesTareaActivity`
   - El título, descripción y estado de la tarea se muestran correctamente
   - La flecha de retroceso vuelve a `MainActivity`

5. **Prueba 4 — Crear tarea:** Toca el FAB (+). Verifica que:
   - Se abre `CrearTareaActivity`
   - El botón "Guardar" está deshabilitado inicialmente
   - Al escribir solo el título, el botón sigue deshabilitado
   - Al escribir título y descripción, el botón se habilita
   - Al borrar el título, el botón se deshabilita y aparece el error
   - Selecciona "En Progreso" en el Spinner
   - Escribe título y descripción, toca "Guardar"
   - Vuelves a `MainActivity` y la nueva tarea aparece en la lista

6. **Prueba 5 — Verificar filtro con nueva tarea:**
   - Toca "En Progreso" en los filtros
   - La tarea recién creada con estado "En Progreso" debe aparecer

7. Repite las pruebas en un AVD con **API 37** para verificar compatibilidad.

8. Verifica que el `AndroidManifest.xml` final tiene la estructura correcta:

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
        android:theme="@style/Theme.TaskManagerUI"
        tools:targetApi="37">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".CrearTareaActivity"
            android:exported="false"
            android:parentActivityName=".MainActivity" />

        <activity
            android:name=".DetallesTareaActivity"
            android:exported="false"
            android:parentActivityName=".MainActivity" />

    </application>

</manifest>
```

### Resultado Esperado

Todas las 5 pruebas funcionales pasan exitosamente:

| Prueba | Resultado Esperado |
|---|---|
| Lista principal | 5 tareas visibles en tarjetas con título, descripción y estado |
| Filtros | Cada filtro muestra solo las tareas correspondientes |
| Navegación a detalles | Datos correctos, retroceso funcional |
| Crear tarea | Validación en tiempo real, guardado exitoso, retorno a lista |
| Filtro post-creación | Nueva tarea visible bajo el filtro correcto |

### Verificación

- [ ] La app funciona correctamente en API 30
- [ ] La app funciona correctamente en API 37
- [ ] La navegación FAB → CrearTareaActivity → MainActivity funciona
- [ ] La navegación RecyclerView item → DetallesTareaActivity → MainActivity funciona
- [ ] Los filtros actualizan la lista correctamente
- [ ] La validación con TextWatcher funciona en tiempo real

---

## Validación y Pruebas

Ejecuta las siguientes verificaciones finales para confirmar que el laboratorio está completo:

### Verificación de Estructura del Proyecto

```
app/src/main/
├── java/com/cursokotlin/android/taskmanagerui/
│   ├── adapter/
│   │   └── TareaAdapter.kt
│   ├── model/
│   │   └── Tarea.kt
│   ├── repository/
│   │   └── TareaRepository.kt
│   ├── viewmodel/
│   │   └── TareaViewModel.kt
│   ├── MainActivity.kt
│   ├── CrearTareaActivity.kt
│   └── DetallesTareaActivity.kt
├── res/
│   ├── layout/
│   │   ├── activity_main.xml
│   │   ├── activity_crear_tarea.xml
│   │   ├── activity_detalles_tarea.xml
│   │   └── item_tarea.xml
│   ├── values/
│   │   ├── strings.xml
│   │   ├── colors.xml
│   │   ├── dimens.xml
│   │   └── themes.xml
│   └── drawable/
│       └── ic_add.xml
└── AndroidManifest.xml
```

### Lista de Verificación Final

| # | Criterio | Estado |
|---|---|---|
| 1 | `ConstraintLayout` es el layout raíz en las 3 Activities | ☐ |
| 2 | `LinearLayout` horizontal usado para barra de filtros con `layout_weight` | ☐ |
| 3 | `FrameLayout` usado para superponer RecyclerView y mensaje vacío | ☐ |
| 4 | `RecyclerView` con `TareaAdapter` (ListAdapter), `TareaViewHolder` y `DiffUtil` | ☐ |
| 5 | `TextWatcher` valida formulario en tiempo real | ☐ |
| 6 | `Spinner` para selección de estado en CrearTareaActivity | ☐ |
| 7 | View Binding habilitado y usado en todas las Activities y el Adapter | ☐ |
| 8 | Todos los textos en `strings.xml`, colores en `colors.xml`, dimensiones en `dimens.xml` | ☐ |
| 9 | Tema Material 3 personalizado en `themes.xml` | ☐ |
| 10 | Navegación completa: MainActivity ↔ DetallesTareaActivity, MainActivity → CrearTareaActivity | ☐ |
| 11 | Compilación exitosa sin warnings críticos | ☐ |
| 12 | Funciona en API 30 y API 37 | ☐ |

---

## Solución
