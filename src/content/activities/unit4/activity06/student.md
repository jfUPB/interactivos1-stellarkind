## Solución a la sexta actividad
### 1) ¿Qué es un protocolo de comunicación y por qué es importante en la comunicación serial?
Un protocolo de comunicacíon es un conjunto de reglas que definen cómo se estructuran e interpretan los datos que se envían entre dos sistemas. En la comunicación serial, donde los datos se envían bit a bit a través de un único canal (o un par de canales), el protocolo es súper importante. Me dice cómo están organizados los diferentes datos dentro de una secuencia (por ejemplo, qué byte o conjunto de bytes representa un valor, dónde termina un dato y empieza el siguiente, dónde termina el mensaje completo).   
En mi código, el protocolo que exploramos define que los datos se envían como una cadena de texto, donde los valores están separados por comas, y cada mensaje completo termina con un salto de línea (\n).

En el micro:bit:
```Python
data = "{},{},{},{}\n".format(xValue, yValue, aState,bState) # Define la estructura del mensaje
uart.write(data) # Envía el mensaje siguiendo la estructura definida
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
Por ejemplo:
- En el estado (```WAIT_MICROBIT_CONNECTION```), el sketch solo debe esperar y mostrar un mensaje de conexión.
- En otro estado (```RUNNING```), el sketch debe leer activamente los datos del micro:bit, usar esos datos para el dibujo generativo y responder a los botones del micro:bit.

Estos dos comportamientos son muy diferentes. Una máquina de estados me permite organizar mi código para que la lógica de ```draw()``` solo ejecute el código relevante para el estado actual y gestione las transiciones entre estados.

### 5) ¿Cómo se formatean los datos en el micro:bit para ser enviados por el puerto serial?
En el micro:bit, los datos (en el caso de esta unidad, los valores de acelerómetro y los estados booleanos ```aState```, ```bState```) se convierten en una única cadena de texto. Se utiliza el método ```.format()``` de una cadena para insertar estos valores en una plantilla que contiene marcadores de posición (```{}```). Entre estos marcadores, se colocan comas (```,```) que se convierten en parte de la cadena final, separando los valores. ya al final de la cadena se añade un carácter de salto de línea (```\n```) para indicar el fin del mensaje.
```python
xValue = accelerometer.get_x()
yValue = accelerometer.get_y()
aState = button_a.is_pressed()
bState = button_b.is_pressed()
# Los valores se insertan en la cadena plantilla
data = "{},{},{},{}\n".format(xValue, yValue, aState,bState)
# La cadena resultante se envía
uart.write(data)
```

### 6) ¿Qué significa que los datos enviados por el micro:bit están codificados en ASCII?
Significa que la cadena de texto que el micro:bit crea (```"123,756,False,True\n"``` por ejemplo) se envía a través del cable serial como una secuencia de números. Cada carácter de la cadena original (como '1', '2', '3', ',', 'F', 'a', etc., y el salto de línea ```\n```) se representa por un número estándar específico según la tabla de codificación ASCII.

### 7) ¿Por qué es necesario en la aplicación de p5.js preguntar si hay bytes disponibles en el puerto serial antes de leerlos? ¿Qué pasa si esto no se hace?
Es necesario preguntar si hay bytes disponibles (```if (port.availableBytes() > 0)```) antes de llamar a ```port.readUntil("\n")``` porque ```readUntil()``` es una función que pausa la ejecución del sketch. Entonces usar ```if (port.availableBytes() > 0)```, asegura de que solo intento leer datos si sé primero que al menos ha llegado algo. Esto evita que el sketch se pegue si no hay actividad serial en un momento dado.

### 8) ¿Cómo se elimina el retorno de carro o salto de línea de un string en p5.js?  
según lo que he visto(aunque no me termina de quedar muy claro que es), para eliminar el retorno de carro (```\r```, que a veces acompaña al salto de línea ```\n``` dependiendo del sistema operativo) y el salto de línea, así como cualquier otro espacio en blanco al inicio o al final de una cadena, se usa el método ```.trim()``` de la cadena.

```js
let data = port.readUntil("\n"); // Leo la cadena, incluyendo el \n
if (data) {
  data = data.trim(); // Elimino el \n y otros espacios al inicio/fin
  // ... ahora 'data' no tiene el \n al final ...
}
```

### 9) 
