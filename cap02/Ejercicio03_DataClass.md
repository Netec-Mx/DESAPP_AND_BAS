# Ejerccio adicional: Inventario con data class

<br/>

## Objetivo

Repasar clases, objetos, propiedades y métodos, y comprobar qué aporta una **data class**: representación legible, igualdad por datos y creación de copias.

**Duración:** 10 minutos.  

<br/>
<br/>

### 1. Modelar un producto  

Declara una **data class Producto** con estas propiedades en su constructor principal:

- **nombre:** String.
- **precio:** Double.
- **existencias:** Int, con valor por defecto **0**.

Declara las tres propiedades con **val**. Agrega un método **valorInventario()** que devuelva un Double con el precio multiplicado por las existencias.

Supón que los valores recibidos son válidos.

<br/>
<br/>

### 2. Crear y comparar objetos  

Desde **main**:

- Crea dos objetos distintos con los mismos datos: **"Teclado"**, precio **250.0** y **4** existencias.
- Imprime uno directamente. Comprueba que aparecen el nombre de la clase y sus propiedades sin escribir tu propio toString().
- Compara ambos con **==** y con **===**. Resultados esperados: **true** y **false**, respectivamente.
- Invoca valorInventario() en uno. Resultado esperado: **1000.0**.

<br/>
<br/>

### 3. Crear una variante con copy()  

A partir del primer producto:

- Utiliza **copy()**, con argumentos con nombre, para crear otro con precio **200.0** y **3** existencias.
- Conserva el nombre sin volver a escribirlo en la llamada.
- Imprime el original y la copia. Comprueba que el original conserva precio **250.0** y existencias **4**.
- Calcula el valor del inventario de la copia. Resultado esperado: **600.0**.
- Compara el original y la copia con **==**. Resultado esperado: **false**.

<br/>
<br/>

### 4. Comprobar y explicar  

Crea un producto **"Mouse"**, precio **150.0**, omitiendo las existencias. Su valor de inventario debe ser **0.0**.

Responde brevemente:

1. ¿Cuál es la clase y cuáles son sus objetos?
2. ¿Por qué dos objetos distintos pueden ser iguales con ==?
3. ¿Por qué copy() permite obtener otro precio aunque la propiedad esté declarada con val?

<br/>
<br/>

## Resultado esperado

Un programa ejecutable que muestre los productos, las comparaciones y los valores de inventario indicados.

<br/>

**Recuerda:** == compara igualdad mediante equals(); === compara si se trata del mismo objeto. En este ejercicio, la `data class` genera la comparación de datos a partir de las propiedades de su constructor principal.



