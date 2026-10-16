# Ejercicio adicional: Precios flexibles con lambdas

<br/>

## Objetivo

Declarar e invocar lambdas, pasarlas como argumentos y utilizar **it** y la notación de **lambda final** (*trailing lambda*).

**Duración:** 12 minutos.  

<br/><br/>

### 1. Guardar comportamientos  

Dentro de **main**, declara dos variables con **val**, ambas de tipo **(Double) -> Double**, y asígnales lambdas:

- **descuento20:** recibe un precio y devuelve ese precio con un descuento del **20 %**.

- **recargo30:** recibe un precio y devuelve ese precio más un cargo fijo de **30.0**.

Invoca cada lambda directamente con **200.0** y muestra los resultados:

- descuento20 → **160.0**.
- recargo30 → **230.0**.

Utiliza inicialmente un nombre explícito para el parámetro de cada lambda.

<br/><br/>

### 2. Recibir una lambda en una función  

Fuera de main, declara la función **calcularPrecio** con estos parámetros, en este orden:

- **precioBase:** Double.
- **ajuste:** (Double) -> Double.

La función debe invocar ajuste con precioBase y devolver el Double obtenido. No debe contener reglas específicas de descuentos ni recargos.

<br/>

Desde main, invócala dos veces con precio base **200.0**:

- Pasa la lambda guardada en descuento20 → **160.0**.
- Pasa la lambda guardada en recargo30 → **230.0**.

Pasa la lambda como comportamiento; no pases el resultado de ejecutarla previamente.

<br/><br/>

## 3. Usar it y lambda final 

- Reescribe la lambda asignada a descuento20 utilizando **it**, sin declarar explícitamente su parámetro. Comprueba que el resultado sigue siendo **160.0**.
- Invoca calcularPrecio con **200.0** y una nueva lambda escrita directamente en la llamada, fuera de los paréntesis.
- Esta nueva lambda debe devolver la mitad del precio. Utiliza **it**. Resultado esperado: **100.0**.
- No crees otra variable para esta última lambda.

<br/><br/>

### 4. Explicar lo realizado 

Responde brevemente:

1. ¿Qué representa el tipo (Double) -> Double?
2. ¿Por qué calcularPrecio es una función de orden superior?
3. ¿Por qué la última lambda puede escribirse fuera de los paréntesis?


<br/><br/>

## Comprobación

- Las lambdas devuelven números; los resultados se imprimen desde main.
- calcularPrecio ejecuta el comportamiento que recibe.
- Se comprueban los resultados **160.0**, **230.0** y **100.0**.
- Se utiliza al menos una lambda con parámetro explícito, otra con **it** y una llamada con **lambda final**.

<br/><br/>

**Recordatorio:** en estas lambdas, la última expresión determina el valor devuelto. **it** permite referirse al único parámetro cuando su tipo se puede inferir. La lambda puede ir fuera de los paréntesis cuando corresponde al último parámetro de la función y este es de tipo función.

<br/><br/>

## Ejecución  

- Escribe el programa en [Kotlin Playground](https://play.kotlinlang.org/) y pulsa **Run**.


<br/><br/>

## Referencias Adicionales

- Consulta la [documentación oficial de funciones de orden superior y lambdas](https://kotlinlang.org/docs/lambdas.html).

