# Laboratorio adicional 5.2 — Galería aleatoria

<br/>

## Descripción general

Construirás una aplicación Android de una sola pantalla que muestra una fotografía local. Cada vez que toques la pantalla, aparecerá otra imagen elegida al azar. Utilizarás entre cinco y seis fotografías guardadas en `res/drawable`, un arreglo de identificadores de recursos y un `ImageView`.

Esta práctica es una introducción breve al manejo de imágenes antes o después del Laboratorio 5.1. Las fotografías ya forman parte de la aplicación: no se seleccionan de la galería del teléfono.

<br/>

## Objetivos de aprendizaje

Al terminar podrás:

- Incorporar imágenes locales a `res/drawable` y acceder a ellas con `R.drawable`.
- Presentar imágenes en un `ImageView` y elegir cómo ajustarlas a la pantalla.
- Utilizar un `IntArray` de identificadores de recursos.
- Atender un toque sobre la pantalla para seleccionar otra imagen aleatoria.
- Evitar que una imagen se repita en dos cambios consecutivos.

<br/>

## Prerrequisitos

- Kotlin básico: variables, arreglos, funciones y condiciones.
- Uso básico de Android Studio, una Activity y diseños XML.
- Un emulador o dispositivo Android configurado.

No necesitas el proyecto TaskManager ni agregar bibliotecas o permisos.

<br/><br/>

## Instrucciones

### Paso 1 — Crear el proyecto

1. En Android Studio, crea un proyecto llamado `Galeria` con lenguaje **Kotlin** y una plantilla que genere una Activity con interfaz **Views/XML** (por ejemplo, **Empty Views Activity**, si aparece en tu versión).

2. Utiliza el SDK mínimo que ya empleas en el curso, por ejemplo API 30.

3. Confirma que existen `MainActivity.kt` y `app/src/main/res/layout/activity_main.xml`. Si tu plantilla utiliza Compose, vuelve a crear el proyecto con una plantilla de Views.

**Resultado esperado:** el proyecto inicial se compila y ejecuta.

<br/>

### Paso 2 — Conseguir e incorporar las imágenes

1. Descarga cinco o seis fotografías gratuitas, preferiblemente de un mismo tema: naturaleza, ciudades, animales o arquitectura. Consulta las fuentes al final del laboratorio y revisa la licencia de cada fotografía antes de distribuir el proyecto.

2. Elige archivos JPG o PNG de tamaño moderado. Para esta práctica, puedes reducir las fotografías a unos 1080 píxeles en su lado mayor para evitar que el proyecto crezca demasiado. 

3. Cambia sus nombres a `foto_1.jpg`, `foto_2.jpg`, `foto_3.jpg`, `foto_4.jpg`, `foto_5.jpg` y, si corresponde, `foto_6.jpg`.

4. Copia los archivos en `app/src/main/res/drawable/` (`drawable`, en singular). Si Android Studio ya muestra esa carpeta en la vista **Android**, puedes pegar allí las imágenes.

5. Comprueba que aparecen como recursos y que no hay nombres con espacios, mayúsculas, acentos o guiones medios.

**Resultado esperado:** cada fotografía tiene un identificador como `R.drawable.foto_1`. La extensión `.jpg` no se escribe al referirse al recurso desde Kotlin.



<br/>
<br/>

### Paso 3 — Diseñar la pantalla

Reemplaza el contenido de `app/src/main/res/layout/activity_main.xml` por el siguiente diseño. La pantalla completa es la superficie táctil; el texto inferior indica qué hacer.

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pantallaGaleria"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#171717">

    <ImageView
        android:id="@+id/imagenGaleria"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:contentDescription="@string/descripcion_inicial"
        android:scaleType="centerCrop"
        android:src="@drawable/foto_1" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#99000000"
        android:gravity="center"
        android:padding="20dp"
        android:text="@string/instruccion_galeria"
        android:textColor="#FFFFFF"
        android:textSize="18sp" />

</FrameLayout>
```

<br/>

Agrega estas cadenas en `app/src/main/res/values/strings.xml`, dentro de `<resources>`:

```xml
<string name="instruccion_galeria">Toca la pantalla para cambiar de imagen</string>
<string name="descripcion_inicial">Imagen de la galería</string>
<string name="descripcion_imagen">Imagen %1$d de %2$d de la galería</string>
```

`centerCrop` llena el espacio sin deformar la fotografía, aunque puede recortar sus bordes. Como comparación, prueba `fitCenter` y observa las áreas libres que pueden aparecer.


<br/>

### Paso 4 — Crear el arreglo de recursos y cambiar la imagen

Abre `MainActivity.kt`. Conserva la declaración `package` generada para tu proyecto y coloca este código debajo de ella. Si la plantilla incluyó otros `import`, consérvalos únicamente cuando sean necesarios.

```kotlin
import android.os.Bundle
import android.view.View
import android.widget.ImageView
import androidx.appcompat.app.AppCompatActivity
import kotlin.random.Random

class MainActivity : AppCompatActivity() {

    private val imagenes = intArrayOf(
        R.drawable.foto_1,
        R.drawable.foto_2,
        R.drawable.foto_3,
        R.drawable.foto_4,
        R.drawable.foto_5
        // Agrega R.drawable.foto_6 si descargaste una sexta fotografía.
    )

    private var indiceActual = -1
    private lateinit var imagenGaleria: ImageView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        imagenGaleria = findViewById(R.id.imagenGaleria)
        val pantallaGaleria = findViewById<View>(R.id.pantallaGaleria)

        pantallaGaleria.setOnClickListener {
            mostrarImagenAleatoria()
        }

        mostrarImagenAleatoria()
    }

    private fun mostrarImagenAleatoria() {
        var siguienteIndice: Int

        do {
            siguienteIndice = Random.nextInt(imagenes.size)
        } while (imagenes.size > 1 && siguienteIndice == indiceActual)

        indiceActual = siguienteIndice
        imagenGaleria.setImageResource(imagenes[indiceActual])
        imagenGaleria.contentDescription = getString(
            R.string.descripcion_imagen,
            indiceActual + 1,
            imagenes.size
        )
    }
}
```

<br/>

> **Importante:** si tu proyecto genera `ComponentActivity` en lugar de `AppCompatActivity`, puedes conservar esa clase base y su `import`; el resto de la lógica es igual. Si agregas una sexta imagen al arreglo, coloca una coma después de `R.drawable.foto_5`.


**Observa:** los elementos del arreglo son números enteros que identifican recursos, no rutas de archivo. `Random.nextInt(imagenes.size)` produce un índice válido. El ciclo `do…while` elige otro índice cuando sale el mismo que se está mostrando; con cinco o seis 
fotografías terminará normalmente.

<br/>
<br/>

### Paso 5 — Ejecutar y comprobar

1. Ejecuta la aplicación. Debe aparecer una fotografía al iniciar.

2. Toca distintas zonas de la pantalla, incluida la franja inferior. En cada toque debe aparecer una imagen diferente de la que estaba visible.

3. Comprueba que todas las fotografías pueden mostrarse al repetir los toques.

4. Cambia temporalmente `centerCrop` por `fitCenter` y compara el resultado con imágenes de distintas proporciones. Restaura la opción que prefieras.

| Comprobación | Resultado esperado |
| --- | --- |
| Inicio | Aparece una de las fotografías locales. |
| Toque de pantalla | Se muestra otra fotografía. |
| Dos toques consecutivos | No se repite la imagen anterior. |
| Ajuste de imagen | La fotografía mantiene sus proporciones. |


<br/>


## Resumen

Creaste una galería local de una sola pantalla. Incorporaste fotografías como recursos `drawable`, las agrupaste en un `IntArray` y cambiaste la imagen mostrada mediante toques y selección aleatoria. También comparaste dos maneras de ajustar fotografías en un `ImageView`.


<br/>
<br/>

### Recursos adicionales

▸ **Pexels:** fotografías gratuitas; revisa sus condiciones de uso al elegir las imágenes del proyecto.  
[https://www.pexels.com/](https://www.pexels.com/) · [Licencia](https://www.pexels.com/license/)

▸ **Unsplash:** banco de fotografías de distintos temas; consulta la licencia antes de redistribuir archivos.  
[https://unsplash.com/](https://unsplash.com/) · [Licencia](https://unsplash.com/license)

▸ **Pixabay:** fotografías e ilustraciones disponibles bajo su licencia de contenido; revisa las restricciones de cada uso.  
[https://pixabay.com/](https://pixabay.com/) · [Licencia](https://pixabay.com/service/license-summary/)

▸ **Recursos drawable — Android Developers:** cómo guardar imágenes en `res/drawable` y usarlas mediante `@drawable` y `R.drawable`.  
[https://developer.android.com/guide/topics/resources/drawable-resource?hl=es-419](https://developer.android.com/guide/topics/resources/drawable-resource?hl=es-419)

▸ **Drawables vectoriales — Android Developers:** tutorial para crear recursos XML con `<vector>` y `<path>`, incluido `android:pathData`; útil para iconos, no para sustituir fotografías.  
[https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources?hl=es](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources?hl=es)

▸ **Degradados en XML — Android Developers:** consulta la sección **Shape Drawable** y su elemento `<gradient>` para crear fondos con transiciones de color.  
[https://developer.android.com/guide/topics/resources/drawable-resource?hl=es-419#Shape](https://developer.android.com/guide/topics/resources/drawable-resource?hl=es-419#Shape)

▸ **Gradientes en `VectorDrawable` — Android Developers:** referencia de `<gradient>` para rellenos dentro de vectores, si deseas explorar esta variante después del laboratorio.  
[https://developer.android.com/reference/android/graphics/drawable/VectorDrawable](https://developer.android.com/reference/android/graphics/drawable/VectorDrawable)
