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
