## Solución a la cuarta actividad
### Experimentando con el sketch a través de la comunicación serial
![image](https://github.com/user-attachments/assets/2bf517f7-5046-44e5-956e-41df36c120ca)

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
Al sumar ```windowWidth/2``` y ```windowHeight/2```, estoy centrando las coordenadas del micro:bit en el medio de mi lienzo. Así, cuando el micro:bit está plano (X=0, Y=0), mi posición de dibujo estará en el centro de la ventana. Inclinar el micro:bit moverá la posición de dibujo desde ese centro, osea que sirve para mapear la inclinación del microbit.

### ¿Cómo puedes verificar que los eventos de keypressed y keyreleased se están generando?
La forma en que yo puedo verificarlo con el código que construimos es observando la consola del navegador mientras ejecuto mi sketch y presiono los botones del micro:bit.

### ```updateButtonStates```: ¿Qué hace? ¿Por qué es necesario almacenar el estado anterior de los botones? ¿Qué pasaría si no se almacenara el estado anterior?
El propósito de ```updateButtonStates``` es tomar los estados actuales de los botones que acabo de leer del micro:bit y usarlos para simular los eventos de "botón presionado una vez" o "botón soltado una vez", de manera similar a cómo funcionaban mousePressed() o keyReleased() en el sketch original para el mouse. Es absolutamente necesario almacenar el estado anterior de los botones dado que la función ```draw()``` se ejecuta muchas veces por segundo, si no se almacenara el estado anterior, presionar y mantener el botó haría que el tamaño del pincel cambiase aleatoriamente y su posición de inicio se reseteara en cada fotograma, lo que haría muy dificil dibujar con la herramienta.

### Compara el código original y el nuevo código
Lo principal:
- Se añadió todo el código para para la comunicación serial con el micro:bit y la lecturas serial en ```draw()```.
- Se implementó una máquina de estados simple para controlar el flujo de la aplicación (esperar conexión y dibujar).
- Se añadió la función ```updateButtonStates()``` junto con unas variables para procesar los estados de los botones del micro:bit y generar eventos de "presionar una vez" y "soltar una vez".
-  En el sketch original, el dibujo comenzaba al presionar el mouse. Ahora la lógica de iniciar el dibujo se movió dentro del estado ```STATES.RUNNING``` y está controlada por si el botón A del micro:bit está presionado. La función ```mousePressed()``` en este código nuevo solo se usa para el botón "Connect/Disconnect". De la misma forma la función de cambiar a color aleatorio que antes se activaba al soltar la barra espaciadora ahora se activa cuando se detecta que el botón B del micro:bit acaba de ser soltado. El micro:bit tomó el control de algunas acciones que antes hacían el mouse y el espacio, mientras otras funciones de teclad siguen igual.

### Al ejecutar y mirar en consola sale el mensaje "No se están recibiendo 4 datos del micro:bit" ¿Qué significa esto? ¿este mensaje ocurre en varios frames o solo en uno? ¿Por qué? ¿Qué puedes hacer para solucionar este problema?
Viendo el código resalta de donde viene el mensaje en la funcion draw:
``` js
if (values.length == 4) {
          microBitX = int(values[0]) + windowWidth / 2;
          microBitY = int(values[1]) + windowHeight / 2;
          microBitAState = values[2].toLowerCase() === "true";
          microBitBState = values[3].toLowerCase() === "true";
          updateButtonStates(microBitAState, microBitBState);
        } else {
          print("No se están recibiendo 4 datos del micro:bit");
        }
```
Que según entiendo es que se manda el mensaje cada que el programa logra recibir una linea de datos del microbit, pero que intentando ponerle las comas entre los datos el número de datos que reconoce no es exactamente 4 datos, lo cual es muy raro porque el microbit siempre envía los 4 datos separados por comas (según revisamos con la aplicación de conexión serial, aunque luego de experimentar más no se si por error del microbit que estoy usando de la nada deja de enviar datos o al menos no aparecen en el programa de recepción de datos, no se si quizá eso tendrá algo que ver), entonces es que debe haber algún problema al recibir esa linea de datos, ya que casi siempre el mensaje aparece apenas uno conecta el microbit, pero no logro identificar un patrón aparente que genere que salga el mensaje, solo sale de forma ocasional, de vez en cuando, mientras el micro:bit está enviando datos. Así que probablemente no tenga nada que ver con el código sino con la comunicación serial como tal que puede tener problemas al ser tan directa por el tipo de comunicación tan en tiempo real que estamos utilizando, entonces el mensaje es resultado de la saturación del canal de comunicación por el poco control que tenemos sobre el timing de el intercambio de datos.
