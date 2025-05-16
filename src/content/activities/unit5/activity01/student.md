## Solución a la primer actividad
### Describe cómo se están comunicando el micro:bit y el sketch de p5.js. ¿Qué datos envía el micro:bit?
El micro:bit y el sketch de p5.js se están comunicando a través de comunicación serial usando un cable USB conectado a la computadora. El micro:bit actúa como el dispositivo que envía datos (el emisor), y el sketch de p5.js que corre en el navegador recibe esos datos (el receptor) a través del puerto serial que p5.js puede acceder gracias a la librería p5.webserial.
El micro:bit envía los siguientes datos: El valor de la aceleración en el eje X (xValue), el valor de la aceleración en el eje Y (yValue), el estado del botón A (si está presionado o no, aState) y el estado del botón B (si está presionado o no, bState). Envía estos 4 datos empaquetados en un solo mensaje, 10 veces por segundo (sleep(100) en el código del micro:bit).

### Cómo es la estructura del protocolo ASCII usado?
El protocolo ASCII es un protocolo basado en texto donde una secuencia de valores se  separan por comas y se termina por un carácter de salto de linea.

### Muestra y explica la parte del código de p5.js donde lee los datos del micro:bit y los transforma en coordenadas de la pantalla.
La parte del código en ```sketch.js``` que lee y procesa los datos seriales está dentro de la función ```draw()```, específicamente en el bloque que se ejecuta cuando el puerto serial está abierto:
``` js
function draw() {
  // ... (código de manejo de conexión) ...

  if (port.opened()) { // Si el puerto está abierto...
    // ... (código de actualización del botón) ...

    // *** Parte que lee los datos seriales ***
    if (port.availableBytes() > 0) { // 1. Pregunta si hay algún byte disponible
      let data = port.readUntil("\n"); // 2. Lee datos hasta encontrar un salto de línea
      if (data) { // 3. Verifica que se leyó algo válido
        data = data.trim(); // 4. Elimina espacios al inicio/fin (incluido el \n)
        let values = data.split(","); // 5. Divide la cadena en un array usando la coma
        if (values.length == 4) { // 6. Verifica que el array tiene 4 elementos (el protocolo)
          // 7. Transforma los datos y los guarda en variables globales
          microBitX = int(values[0]) + windowWidth / 2; // Convierte X a entero y lo centra
          microBitY = int(values[1]) + windowHeight / 2; // Convierte Y a entero y lo centra
          microBitAState = values[2].toLowerCase() === "true"; // Convierte cadena "True"/"False" a booleano
          microBitBState = values[3].toLowerCase() === "true"; // Convierte cadena "True"/"False" a booleano
          updateButtonStates(microBitAState, microBitBState); // Procesa los estados de los botones
        } else {
          print("No se están recibiendo 4 datos del micro:bit"); // Mensaje de error si no cumple el protocolo
        }
      }
    }
    // ***************************************
  }

  // ... (código de máquina de estados y dibujo) ...
}
```
### ¿Cómo se generan los eventos A pressed y B released que se generan en p5.js a partir de los datos que envía el micro:bit?
Los eventos "A pressed" y "B released" (y las acciones asociadas) no se generan automáticamente como eventos de teclado o ratón de p5.js. Se simulan y detectan comparando el estado actual de los botones recibido del micro:bit con el estado que tenían en el frame anterior.

### 
