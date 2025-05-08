## Solución a la cuarta actividad

### Compara el código original del caso de estudio con el anterior. ¿Qué notas de diferente?
Se añadió una nueva línea ```<script src="https://unpkg.com/@gohai/p5.webserial@^1/libraries/p5.webserial.js"></script>```. Esto es muy importante, porque esta línea carga la librería p5.webserial, que es la que me va a permitir que mi sketch en p5.js se comunique con dispositivos seriales como el micro:bit a través del puerto USB, la que ya hemos sado en actividades anteriores.  

###  Reflexiona ¿Para qué se usan estas imágenes? ¿Qué representan?
Ejecuté el sketch. No hubo errores aparentes. Las imágenes se cargan en el array ```lineModule``` de la función ```preload()```, por lo qu creo que estarán listas para ser utilizadas por el sketch antes de que empiece a dibujar. Revisando el sketch original de nuevo, recordé que estas imágenes son las "formas" que dibuja el pincel generativo cuando no se selecciona la línea simple.

### ¿Puedes notar que no tenemos a INIT en este caso? Pero la verdad si está, solo que no como antes, la función setup() está haciendo esa función. ¿Lo ves?
Ajá, en unidades anteriores teníamos un pseudoestado INIT, pero aquí no lo tenemos explícitamente. En este caso la función ```setup()``` actúa como ese estado INIT, haciendo el trabajo de inicialización.

### ¿Qué pasaría ahora si das click al botón?
Al ejecutar la aplicación, apareció el botón "Connect to micro:bit" en la esquina superior izquierda. Al hacer clic, la consola me mostraba mensajes indicando si se conectaba o desconectaba, dependiendo del estado del puerto. Sin embargo, el texto del botón no cambiaba automáticamente después de conectarse.

### Nota que solo se leen datos del micro:bit si el puerto está abierto ¿Por qué? ¿Podrías leer datos si el puerto está cerrado? ¿Qué pasaría si el puerto está cerrado y el micro:bit envía datos?
Creería yo qur no puedo recibir información a través de una conexión que no está activa. Si el puerto está cerrado y el micro:bit sigue enviando datos, esos datos simplemente se pierden; no hay ninguna parte de mi programa en p5.js que esté guardando esos datos mientras el puerto está cerrado.

### ¿Qué pasaría si el micro:bit no envía el "\n"?
Si el micro:bit no enviara el ```\n```, la función ```readUntil()``` seguiría esperando indefinidamente a que llegue ese carácter, y mi sketch se quedaría trabado en esa línea.

### ¿Por qué se suma ```windowWidth/2``` y ```windowHeight/2``` a los valores de x e y?
 Al sumar windowWidth/2 y windowHeight/2, estoy centrando las coordenadas del micro:bit en el medio de mi lienzo. Así, cuando el micro:bit está plano (X=0, Y=0), mi posición de dibujo estará en el centro de la ventana. Inclinar el micro:bit moverá la posición de dibujo desde ese centro, osea que sirve para mapear la inclinación del microbit.

 ### 

 
