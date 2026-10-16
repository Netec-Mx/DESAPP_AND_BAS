# Ejercicio: Funciones para una compra

<br/>

## Objetivo

Practicar la declaración e invocación de funciones, los argumentos con nombre, los valores por defecto y null safety.

**Duración:** 15 minutos.

<br/>

## Instrucciones

Crea un archivo **Funciones.kt**. Declara las siguientes funciones e invócalas desde **main**. Las funciones deben devolver resultados; muéstralos desde main.

<br/>

### 1. Calcular el total

Crea **calcularTotal** con estos parámetros:

- **precio:** Double.
- **cantidad:** Int, con valor por defecto **1**.
- **descuento:** Double, con valor por defecto **0.0**, expresado como porcentaje.

Devuelve un **Double**: multiplica el precio por la cantidad y aplica el descuento al subtotal. Supón que los datos son válidos.

<br/>

Realiza estas pruebas:

- Precio **100.0**, omitiendo cantidad y descuento → **100.0**.
- Precio **100.0**, cantidad **2** y descuento **10.0**, con argumentos por posición → **180.0**.
- Repite la prueba anterior con todos los argumentos con nombre y en otro orden → **180.0**.
- Precio **100.0** y descuento **20.0**, omitiendo cantidad → **80.0**.

<br/>
<br/>

### 2. Personalizar el saludo

Crea **crearSaludo**. Recibe **nombre**, de tipo **String?**, sin valor por defecto, y devuelve un **String**:

- Si recibe **"Hugo"** → **"Hola, Hugo"**.
- Si recibe **null** → **"Hola, visitante"**.

Resuelve el caso nulo sin utilizar **!!**.

<br/>

### Comprobación

- Guarda un resultado en **val**.
- Declara un nombre con **var**, úsalo para obtener un saludo, asígnale null y vuelve a invocar la función.
- Comprueba que todos los resultados coincidan con los indicados.

<br/>
<br/>


## Ejecutar

- **En línea:** usa [Kotlin Playground](https://play.kotlinlang.org/) y pulsa **Run**.

- **Con kotlinc:** con el JDK y los comandos kotlinc y java disponibles en PATH, abre una terminal en la carpeta del archivo:

```cmd
kotlinc Funciones.kt -include-runtime -d funciones.jar
java -jar funciones.jar
```

Después de modificar el archivo, vuelve a compilar.

<br/>
<br/>

## Referencias adicionales

- [Funciones, argumentos con nombre y valores por defecto](https://kotlinlang.org/docs/functions.html).

- [Null safety](https://kotlinlang.org/docs/null-safety.html).

