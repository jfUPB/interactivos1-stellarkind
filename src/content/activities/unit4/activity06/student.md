## Solución a la sexta actividad
### 1) ¿Qué es un protocolo de comunicación y por qué es importante en la comunicación serial?
Un protocolo de comunicacíon es un conjunto de reglas que definen cómo se estructuran e interpretan los datos que se envían entre dos sistemas. En la comunicación serial, donde los datos se envían bit a bit a través de un único canal (o un par de canales), el protocolo es súper importante. Me dice cómo están organizados los diferentes datos dentro de una secuencia (por ejemplo, qué byte o conjunto de bytes representa un valor, dónde termina un dato y empieza el siguiente, dónde termina el mensaje completo).   
En mi código, el protocolo que exploramos define que los datos se envían como una cadena de texto, donde los valores están separados por comas, y cada mensaje completo termina con un salto de línea (\n).

En el micro:bit:
```Python
data = "{},{},{},{}\n".format(xValue, yValue, aState,bState) # Define la estructura del mensaje
uart.write(data) # Envía el mensaje siguiendo la estructura definida
Fragmento de mi código (p5.js):
```  
En p5.js:
``` js
let data = port.readUntil("\n"); // Lee hasta encontrar el fin del mensaje
// ...
let values = data.split(","); // Divide el mensaje en partes usando la coma como separador
// ...
if (values.length == 4) { /* ... */ } // Verifica si la estructura del mensaje es correcta (4 partes)
```
### 2) ¿Por qué se separan los datos con comas en el protocolo ASCII que exploramos?
Las comas se usan paraa delimitar, estas le indican al programa que recibe: "Aquí termina un dato y el siguiente empieza justo después". Esto es fundamental para poder separar la única cadena de texto que llega por el serial en los diferentes valores individuales que necesito procesar en p5.js.

### 3) ¿Por qué es necesario terminar los datos con un carácter que marque el fin del mensaje?
Es necesario porque a pesar que los datos llegan como un flujo continuo de bytes, el programa que recibe necesita saber cuándo un paquete completo de informacín. El carácter de fin de mensaje (\n) actúa como el punto final de cada mensaje.  
Mi código en p5.js usa una función que espera específicamente este carácter para saber que ya ha leído una línea completa de datos (```port.readUntil("\n")```). Si el micro:bit no enviara el ```\n```, el sketch de p5.js seguiría leyendo indefinidamente sin saber cuándo detenerse para procesar un mensaje completo, lo que podría causar que se quede esperando o que procese datos incompletos, que incluso tomando esta precaución a veces pasa que el/los canales se saturan y p5.js no puede procesarlos.

### 4) ¿Por qué fue necesario usar una máquina de estados en la aplicación modificada de p5.js?
Fue necesario usar una máquina de estados porque la aplicación tiene diferentes modos de operación que dependen de si el micro:bit está conectado o no. 
- En el estado (```WAIT_MICROBIT_CONNECTION```), el sketch solo debe esperar y mostrar un mensaje de conexión.
- En otro estado (```RUNNING```), el sketch debe leer activamente los datos del micro:bit, usar esos datos para el dibujo generativo y responder a los botones del micro:bit.
Estos dos comportamientos son muy distintos. Una máquina de estados me permite organizar mi código para que la lógica de ```draw()``` solo ejecute el código relevante para el estado actual y gestione las transiciones entre estados.

### 5) 
