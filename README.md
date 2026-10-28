# Limpieza de datos con lenguaje M

Autor: Gastón Gallardo Forneris.

## Objetivo

Limpiar una tabla de ventas escribiendo las transformaciones
directamente en el Editor Avanzado de Power Query.

El código completo está en script_limpieza.md.

## Transformaciones realizadas

1. Convertir la primera fila en encabezados.
2. Quitar espacios iniciales y finales de nombre_producto.
3. Unificar categoria mediante Text.Proper.
4. Excluir los registros cuya categoría estandarizada es Prueba.
5. Asignar tipos de datos a todas las columnas.

El resultado contiene 5 filas, con los identificadores
1, 2, 4, 5 y 7.

## ¿Qué hace el bloque let...in?

let define expresiones con nombres, que usamos como pasos.
Cada paso puede referenciar otro porque su nombre está disponible
dentro del mismo bloque.

in indica cuál de esos resultados devuelve la consulta.
En este script devuelve TiparColumnas, la tabla final.

Las dependencias entre pasos determinan cómo se obtiene el resultado;
no se trata simplemente de ejecutar líneas de arriba hacia abajo.

## ¿Qué significa que M sea Case Sensitive?

El lenguaje distingue mayúsculas y minúsculas al identificar
funciones, pasos y campos. Es una regla de su sintaxis.

Table.TransformColumns es una función válida.
table.transformcolumns no identifica esa función y genera un error.

También se produciría un error si definiéramos LimpiarEspacios
y después intentáramos referenciar limpiarespacios.

## ¿Qué diferencia hay entre Text.Trim y Text.Clean?

Text.Trim elimina espacios en blanco al principio y al final
de un texto. Conserva los espacios entre palabras.

Text.Clean elimina caracteres de control, como tabulaciones
y saltos de línea. No sustituye a Text.Trim para quitar
espacios normales en los extremos.

Usé Text.Trim porque los problemas del ejercicio eran
espacios externos en los nombres de productos.

## ¿Por qué estandarizar antes de filtrar?

La comparación de textos distingue mayúsculas y minúsculas.

Text.Proper convierte PRUEBA, prueba y Prueba en Prueba.
Después, el filtro categoria <> "Prueba" excluye esas variantes
usando una única condición.

Si filtrara primero por "PRUEBA", podrían permanecer registros
escritos como "prueba" o "Prueba".

## Tipos de datos

- id_venta: número entero.
- nombre_producto y categoria: texto.
- precio: número decimal.
- fecha_venta: fecha.

## Fuente

Tabla de siete registros proporcionada en la consigna de
Coderhouse para esta práctica. Los datos están incluidos
en el paso Origen del código M.# lenguaje-m-editor-avanzado
