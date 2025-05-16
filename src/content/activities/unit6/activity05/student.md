## Solución a la quinta actividad
**Mi idea:** Me gustaría expandir en el caso de estudio conectado tres ventanas del navegador en tiempo real. Cada ventana mostrará la posición de las tres ventanas como si fuesen estrellas (cambiaré los círculos por estrellas para hacer analogía a la conexión como constelaciones), me gustaría también que cuado cualquiera de las tres ventanas se mueva, esa ventna se convierta en el centro de la constelación (o pues la estrella que une a las otras dos), así entonces las otras ventanas que no se movieron también se actualizarán para coincidir visualmente con la ventana que sí se movió.

**Info que debería tener en cuenta:** Cada uno de los clientes necesitará enviar su tamaño y posición al servidor. El servidor necesitaría conocer la posición y tamaño de todas las vetanas conectadas y retransmitir esa info a todos los clientes, incluyendo el dato de cuál fue la ventana que envió la última actualización de forma que esta se convierta en el eje de la constelación y todas las ventanas se actualicen visualmente de forma adecuada.

Mi plan de implementación:
1. Para el servidor (```server.js```):
     - Toca modificar para rastrear ya no solo los datos de dos sino de tres clientes. Puede ser util para eso utilizar un objeto para guardar la info de cada cliente identificandolos con su ```socket.id```, de manera que el objeto almacene a qué página perenece cada ```socket.id```.
     - Toca crear una nueva ruta ```/page3``` que sirva ```page3.html```.
     - Puede ser util reemplazar los eventos de cada ventana(```win1/2update```) por un evento general que cualquier cliente pueda enviar con sus datos actuales.
     - Añadir un evento para que cada cliente se identifique (```page1```, ```page2``` o ```page3```), lo que ayudaría a mapear el ```socket.id``` según su tipo de página.
     - Hay que transmitir a todos los clietes un mensaje que contenga el estado actual de todos los clientes conectados, que incluya el ```socket.id``` del cliente qe envió la última actualización.
  2. Para los clientes (```page1```, ```page2```, ```page3```):
