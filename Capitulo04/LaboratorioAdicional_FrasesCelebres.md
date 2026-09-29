# Laboratorio adicional 4.2 — Frases célebres

<br/>

## Descripción general

Crearás una aplicación Android de una sola pantalla. Mostrará una frase y su autor sobre una ilustración de fondo definida en XML. Cada vez que toques la pantalla aparecerá otra frase elegida al azar. Al final cambiarás también el ícono con el que se identifica la aplicación en el dispositivo.

Este laboratorio **opcional**: permite practicar recursos `drawable`, layouts, eventos y colecciones con una interfaz mucho más pequeña. Puedes hacerlo antes o después del 4.1.

<br/>

## Objetivos de aprendizaje

- Representar cada frase mediante una `data class` con `mensaje` y `autor`.
- Guardar varias frases en una colección de Kotlin y elegir una al azar.
- Crear una imagen vectorial de fondo en XML y superponer texto legible.
- Responder al toque de la pantalla mediante un `OnClickListener`.
- Generar y comprobar un nuevo ícono de inicio con Android Studio.

## Prerrequisitos

Conocimientos básicos de proyectos Android con vistas XML, `Activity`, `TextView`, clases de datos y colecciones Kotlin. Usa **Empty Views Activity** con Kotlin y un SDK mínimo disponible en tu entorno; no necesitas terminar TaskManagerUI ni instalar dependencias adicionales.

<br/>

## Instrucciones

### Paso 1 — Crear el proyecto

1. En Android Studio, selecciona **File → New → New Project → Empty Views Activity**. Si aparece **Empty Activity** para Jetpack Compose, busca la plantilla de **Views/XML**.
2. Usa el nombre **FrasesCelebres** y el paquete `com.cursokotlin.android.frasescelebres`. Selecciona Kotlin y crea el proyecto.
3. Ejecuta la aplicación inicial para comprobar que compila. En los siguientes pasos usarás `res/layout/activity_main.xml`, `res/drawable`, `res/values/strings.xml` y `MainActivity.kt`.

**Resultado esperado:** tienes una `MainActivity` que llama a `setContentView(R.layout.activity_main)` y una pantalla inicial creada por la plantilla.

### Paso 2 — Declarar el modelo y la colección

1. Crea `Frase.kt` en el mismo paquete que `MainActivity`:

```kotlin
package com.cursokotlin.android.frasescelebres

data class Frase(
    val mensaje: String,
    val autor: String
)
```

<br/>
<br/>

2. En `MainActivity`, más adelante declararás una lista de objetos `Frase`. En este ejemplo hay cuatro frases breves; puedes agregar otras tras verificar su texto y atribución.

**Observa:** cada elemento de la lista conserva juntos el mensaje y su autor. Así evitarás mostrar un mensaje con un autor distinto.

<br/>
<br/>

### Paso 3 — Crear la imagen de fondo en XML

En `app/src/main/res/drawable`, crea **New → Drawable Resource File** con el nombre `fondo_frases.xml`. Reemplaza su contenido por esta ilustración vectorial sencilla:

```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="360dp"
    android:height="640dp"
    android:viewportWidth="360"
    android:viewportHeight="640">

    <!-- Cielo -->
    <path
        android:fillColor="#234768"
        android:pathData="M0,0 L360,0 L360,640 L0,640 Z" />

    <!-- Sol -->
    <path
        android:fillColor="#F4BF6A"
        android:pathData="M270,105 a52,52 0,1 0,104 0 a52,52 0,1 0,-104 0" />

    <!-- Montañas -->
    <path
        android:fillColor="#466B80"
        android:pathData="M0,430 L85,275 L173,430 L270,305 L360,440 L360,640 L0,640 Z" />
    <path
        android:fillColor="#17354D"
        android:pathData="M0,510 L110,390 L205,515 L300,405 L360,475 L360,640 L0,640 Z" />
</vector>
```

Abre la pestaña **Preview** del recurso y comprueba que aparece un paisaje. Es una imagen definida con trazos en XML; no hace falta descargar una fotografía.

<br/>
<br/>

### Paso 4 — Diseñar la pantalla

Agrega o actualiza estos textos en `res/values/strings.xml` dentro de `<resources>`:

```xml
<string name="app_name">Frases célebres</string>
<string name="titulo_frases">Frases célebres</string>
<string name="instruccion_frases">Toca la pantalla para ver otra frase</string>
```

Reemplaza `res/layout/activity_main.xml` con:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pantallaFrases"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:contentDescription="@string/instruccion_frases"
    android:focusable="true">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:src="@drawable/fondo_frases"
        android:scaleType="centerCrop"
        android:importantForAccessibility="no" />

    <!-- Capa oscura para mejorar el contraste del texto -->
    <View
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#66000000"
        android:importantForAccessibility="no" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:orientation="vertical"
        android:gravity="center"
        android:padding="32dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/titulo_frases"
            android:textColor="#FFFFFF"
            android:textSize="22sp" />

        <TextView
            android:id="@+id/tvMensaje"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="32dp"
            android:gravity="center"
            android:textColor="#FFFFFF"
            android:textSize="28sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/tvAutor"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:gravity="center"
            android:textColor="#FFE0B2"
            android:textSize="18sp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="40dp"
            android:text="@string/instruccion_frases"
            android:textColor="#FFFFFF"
            android:textSize="14sp" />
    </LinearLayout>
</FrameLayout>
```

<br/>

**Observa:** `FrameLayout` apila la imagen, una capa oscura y el contenido. La frase y el autor son dos `TextView` distintos. Al ocupar toda la pantalla, el contenedor raíz será el área que recibe el toque.

<br/>
<br/>

### Paso 5 — Mostrar una frase aleatoria al tocar la pantalla

Reemplaza `MainActivity.kt` con el siguiente código. Conserva el nombre de paquete real si elegiste otro:

```kotlin
package com.cursokotlin.android.frasescelebres

import android.os.Bundle
import android.widget.FrameLayout
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private val frases = listOf(
        Frase("Pienso, luego existo", "René Descartes"),
        Frase("Vine, vi, vencí", "Julio César"),
        Frase("Conócete a ti mismo", "Máxima griega antigua"),
        Frase("El hombre es la medida de todas las cosas", "Protágoras")
    )

    private var indiceActual = -1

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val pantalla = findViewById<FrameLayout>(R.id.pantallaFrases)
        mostrarOtraFrase()

        pantalla.setOnClickListener {
            mostrarOtraFrase()
        }
    }

    private fun mostrarOtraFrase() {
        val opciones = frases.indices.filter { it != indiceActual }
        indiceActual = opciones.random()

        val frase = frases[indiceActual]
        findViewById<TextView>(R.id.tvMensaje).text = frase.mensaje
        findViewById<TextView>(R.id.tvAutor).text = frase.autor
    }
}
```

La primera vez, `indiceActual` vale `-1`, por lo que puede salir cualquiera de las cuatro frases. Después, la selección excluye la frase visible para que **cada toque produzca un cambio**. La colección necesita al menos dos elementos para conservar esta regla. Si tu plantilla usa otra clase base para la actividad, conserva sus importaciones y ajusta únicamente la declaración de la clase.

<br/><br/>

### Paso 6 — Cambiar el ícono de la aplicación

1. En la vista **Android** del panel **Project**, haz clic derecho en `app > res` y elige **New → Image Asset**.

2. En **Icon Type**, selecciona **Launcher Icons (Adaptive and Legacy)**. En **Foreground Layer**, elige un símbolo relacionado con las frases, por ejemplo una burbuja de diálogo mediante **Clip Art**, o importa una imagen propia. Ajusta el tamaño y selecciona un color o imagen en **Background Layer**.

3. Revisa las vistas previas del ícono, incluyendo sus distintas formas. Mantén el nombre `ic_launcher` para sustituir el recurso inicial y completa **Next → Finish**. Acepta el reemplazo de recursos si Android Studio lo solicita.

4. Comprueba que el `AndroidManifest.xml` utilice `android:icon="@mipmap/ic_launcher"`. Si tiene `android:roundIcon`, comprueba también `@mipmap/ic_launcher_round`. Ejecuta la app y revisa su ícono en el lanzador; si el lanzador conserva el anterior, desinstala la versión de prueba e instálala de nuevo.

El ícono del lanzador se genera en recursos `mipmap`; el paisaje de la pantalla permanece en `drawable`. La guía oficial de Image Asset Studio describe esta ruta y las vistas previas de los íconos adaptables.

<br/>

## Validación y pruebas

1. Abre la aplicación: deben verse el paisaje, una frase y su autor.
2. Toca el fondo, el mensaje y el autor: cada toque debe mostrar **otra** pareja de mensaje y autor.
3. Repite varios toques y verifica que nunca se desasocien mensaje y autor.
4. Confirma que el texto sea legible sobre el fondo y revisa el ícono en el lanzador.

**Reto opcional:** conserva la frase visible al girar el dispositivo usando `onSaveInstanceState` y el `Bundle` recibido en `onCreate`. Comprueba antes cómo se comporta la versión inicial al girarlo.

<br/>

## Lo que lograste en este laboratorio

Creaste una aplicación sencilla con una colección de objetos, una selección aleatoria que evita repeticiones inmediatas, una ilustración vectorial XML, contenido superpuesto y un ícono de inicio personalizado.

<br/>

## Recursos adicionales

▸ **Recursos `drawable` de Android:** muestra cómo declarar y usar imágenes y elementos de diseño en XML.  
[https://developer.android.com/guide/topics/resources/drawable-resource](https://developer.android.com/guide/topics/resources/drawable-resource)

▸ **Image Asset Studio:** explica cómo generar y previsualizar íconos de inicio desde Android Studio.  
[https://developer.android.com/studio/write/create-app-icons](https://developer.android.com/studio/write/create-app-icons)
