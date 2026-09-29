# Laboratorio adicional 3.2 — Contador básico y ciclo de vida de una Activity

<br/>
<br/>

## Descripción general

Crearás una aplicación Android con Kotlin: un número en pantalla y dos botones para incrementarlo o decrementarlo. Después observarás en Logcat cuándo se ejecutan los métodos del ciclo de vida de `MainActivity`. Antes de cada recorrido, escribirás en los mensajes del código el orden que esperas ver, ejecutarás la app y corregirás tu predicción con base en lo observado.

Este laboratorio es **adicional y opcional**. Puedes realizarlo para reforzar el capítulo 3; no necesitas completarlo para continuar con los laboratorios principales.

<br/>
<br/>


## Objetivos

Al terminar podrás:

- Construir una pantalla con un `TextView` y dos `Button` mediante XML.
- Implementar las funciones `incrementar()` y `decrementar()` en Kotlin.
- Identificar en Logcat las llamadas a `onCreate`, `onStart`, `onResume`, `onPause`, `onStop`, `onRestart` y `onDestroy`.
- Predecir y comprobar la secuencia de llamadas al abrir la app, enviarla a segundo plano, regresar y salir.

<br/>
<br/>

## Prerrequisitos

- Android Studio y un emulador o dispositivo configurado.
- Conocimientos básicos de proyectos Android, funciones Kotlin y `findViewById`.
- Capacidad para ejecutar una app y abrir Logcat. No se requieren bibliotecas adicionales.

<br/>
<br/>

## Instrucciones

### Paso 1 — Crear el proyecto

1. En Android Studio, selecciona **File → New → New Project**.
2. Elige **Empty Views Activity** para trabajar con una interfaz XML. Si la plantilla ofrece una vista previa, comprueba que genere `activity_main.xml`.
3. Configura el proyecto:

   | Campo | Valor sugerido |
   |---|---|
   | Nombre | `ContadorBasico` |
   | Paquete | `com.cursokotlin.android.contadorbasico` |
   | Lenguaje | Kotlin |
   | Minimum SDK | API 30, o el nivel usado en el curso |

4. Espera a que termine la sincronización de Gradle. Ubica `MainActivity.kt` y `app/src/main/res/layout/activity_main.xml`.

**Resultado esperado:** el proyecto se abre sin errores y contiene una actividad principal con su archivo de diseño XML.

<br/>
<br/>

### Paso 2 — Crear la interfaz

Abre `activity_main.xml` en modo **Code** y reemplaza su contenido con lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:id="@+id/tvContador"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:text="0"
        android:textSize="48sp"
        android:contentDescription="Valor del contador" />

    <Button
        android:id="@+id/btnIncrementar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Incrementar (+)" />

    <Button
        android:id="@+id/btnDecrementar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Decrementar (−)" />

</LinearLayout>
```

<br/>

El `TextView` muestra el contador: no es un campo editable. El valor cambiará mediante los botones.

**Verificación:** en la vista previa aparecen el número `0` y dos botones.

<br/>
<br/>

### Paso 3 — Programar el contador y los mensajes del ciclo de vida

Reemplaza el contenido de `MainActivity.kt` por el siguiente código. Si elegiste otro nombre de paquete, ajusta la primera línea.

```kotlin
package com.cursokotlin.android.contadorbasico

import android.os.Bundle
import android.util.Log
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private val etiquetaLog = "CicloContador"
    private var contador = 0
    private lateinit var tvContador: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(etiquetaLog, "? onCreate")

        setContentView(R.layout.activity_main)
        tvContador = findViewById(R.id.tvContador)

        findViewById<Button>(R.id.btnIncrementar).setOnClickListener {
            incrementar()
        }

        findViewById<Button>(R.id.btnDecrementar).setOnClickListener {
            decrementar()
        }

        mostrarContador()
    }

    private fun incrementar() {
        contador++
        mostrarContador()
    }

    private fun decrementar() {
        contador--
        mostrarContador()
    }

    private fun mostrarContador() {
        tvContador.text = contador.toString()
    }

    override fun onStart() {
        super.onStart()
        Log.d(etiquetaLog, "? onStart")
    }

    override fun onResume() {
        super.onResume()
        Log.d(etiquetaLog, "? onResume")
    }

    override fun onPause() {
        super.onPause()
        Log.d(etiquetaLog, "? onPause")
    }

    override fun onStop() {
        super.onStop()
        Log.d(etiquetaLog, "? onStop")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d(etiquetaLog, "? onRestart")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(etiquetaLog, "? onDestroy")
    }
}
```

<br/>

**Nota:** si tu proyecto genera una actividad que hereda de `ComponentActivity` en vez de `AppCompatActivity`, conserva la clase base generada y retira el import de `AppCompatActivity`. El resto del ejercicio funciona de la misma manera.

<br/>

**Verificación:** ejecuta la app. Debe iniciar en `0`; el botón **Incrementar (+)** suma uno y **Decrementar (−)** resta uno. Por ejemplo: `0 → 1 → 2 → 1`.


<br/>
<br/>

### Paso 4 — Preparar Logcat

1. Abre **View → Tool Windows → Logcat**.
2. Selecciona el dispositivo y el proceso de `ContadorBasico`. Filtra por la etiqueta `CicloContador`. Si no aparece el filtro de etiquetas, busca ese texto en Logcat.
3. Mantén visible Logcat durante cada recorrido. Limpia los registros antes de empezar uno nuevo, o identifica con claridad dónde comienza.

Los signos `?` del código son espacios para **tu predicción**: reemplázalos por números, recompila y ejecuta. Anota el orden real que aparezca en Logcat. Cuando una predicción sea incorrecta, cambia los números, vuelve a ejecutar y explica por qué tu orden inicial no correspondía al recorrido observado.

El **tag** del laboratorio es `CicloContador`.

En el cuadro de búsqueda de Logcat escribe:

```text
tag:CicloContador
```

Así verás los mensajes que registramos con `Log.d(etiquetaLog, ...)`, como `onCreate`, `onStart` y `onResume`.

<br/>
<br/>

### Paso 5 — Recorrido A: abrir la aplicación

1. Cierra la app mediante **Atrás** si estaba abierta. Vuelve a iniciarla desde Android Studio.
2. **Antes de ejecutarla**, escribe en los mensajes de `onCreate`, `onStart` y `onResume` los números `1`, `2` y `3` en el orden que predices. Deja `?` en los demás métodos.
3. Ejecuta la app y compara los mensajes con Logcat.
4. Si el orden no coincide, corrige la numeración y repite el recorrido.

**Registro de observación:**

| Predicción | Orden observado en Logcat | Corrección y explicación |
|---|---|---|
|  |  |  |

<br/>
<br/>

### Paso 6 — Recorrido B: ir a Inicio y volver

1. Con la app visible, pulsa **Inicio** para enviarla a segundo plano. Espera a que aparezcan los mensajes; luego vuelve a la app desde la pantalla de aplicaciones recientes o su icono.
2. Considera **solo los mensajes emitidos desde el momento en que pulsaste Inicio**. Predice la secuencia completa y numera los mensajes correspondientes en el código. Un método que ya apareció al abrir la app puede volver a aparecer: sus números se refieren a este recorrido, no al anterior.
3. Compila y ejecuta de nuevo para comprobar tu predicción. Pulsa **Inicio**, vuelve a la app y compara con Logcat.
4. Corrige los números que no coincidan y repite hasta poder explicar qué ocurre al salir de la pantalla y al regresar.

**Registro de observación:**

| Predicción | Orden observado en Logcat | Corrección y explicación |
|---|---|---|
|  |  |  |

**Pista:** según cómo abandones la pantalla, podrías observar solo una pausa temporal o que la actividad llegue a detenerse. Para este recorrido usa **Inicio**, espera unos segundos y describe exactamente lo que mostró tu dispositivo.

<br/>
<br/>

### Paso 7 — Recorrido C: salir con Atrás

1. Con la app visible, predice y numera los mensajes que aparecerán **a partir del momento en que pulses Atrás**.
2. Compila, abre la app y pulsa **Atrás** una vez. En dispositivos con navegación por gestos, usa el gesto de volver.
3. Compara la predicción con Logcat, corrige y repite si hace falta.

**Registro de observación:**

| Predicción | Orden observado en Logcat | Corrección y explicación |
|---|---|---|
|  |  |  |

**Atención:** observa lo que sucede en tu ejecución. Android no garantiza que `onDestroy` se llame cuando el sistema termina un proceso en segundo plano; por eso no debes usarlo como señal general de que una app siempre se cerró. Si Atrás no cierra la actividad en tu dispositivo, registra ese comportamiento y realiza la prueba de cierre desde la actividad principal sin pantallas intermedias.


<br/>
<br/>

### Paso 8 — Comprobar el comportamiento del contador

1. Incrementa el contador hasta `3` y decrementa una vez: debe mostrar `2`.
2. Pulsa **Inicio** y regresa: observa si el valor continúa en `2` mientras la misma actividad sigue viva.
3. Sal con **Atrás** y abre la app de nuevo: el valor debe iniciar en `0` en una nueva instancia.
4. Anota qué observaste; distingue entre volver a una actividad existente y crear otra instancia.

El ejemplo guarda `contador` solo en una variable de la actividad. Por sencillez, **no restaura el valor después de una recreación**, como una rotación. Conservar ese estado es materia para otro ejercicio.

<br/>
<br/>

### Paso 9 Reto Adicional — Establecer límites y reiniciar el contador

Modifica la aplicación para que el botón **Decrementar (−)** no reduzca el contador por debajo de cero. Agrega un tercer botón, **Restablecer**, que devuelva el valor a cero desde cualquier número. Prueba ambos comportamientos y registra los resultados en tu evidencia.

## Puntos de aprendizaje

- `onCreate` prepara una nueva instancia de la actividad; `onStart` y `onResume` acompañan su entrada a pantalla.
- Al abandonar y recuperar la pantalla pueden intervenir `onPause`, `onStop` y `onRestart`, según la transición realizada.
- Los botones modifican la interfaz, pero pulsarlos **no inicia por sí mismo otro recorrido del ciclo de vida**.
- La numeración se reinicia en cada recorrido: no existe una lista de números que represente todas las posibles ejecuciones de la app.
- El estado guardado solo en una propiedad de `MainActivity` puede perderse si Android recrea la actividad.

<br/>

## Recursos adicionales

- [Ciclo de vida de una actividad — Android Developers](https://developer.android.com/guide/components/activities/activity-lifecycle) — explica los callbacks y las transiciones al abrir, abandonar y recuperar una actividad.
- [Logcat — Android Developers](https://developer.android.com/studio/debug/logcat) — guía para observar y filtrar los mensajes de la aplicación durante la ejecución.
- [Ciclo de Vida](https://developer.android.com/guide/components/activities/activity-lifecycle?hl=es-419) — Diagrama oficial del ciclo de vida de un Activity
