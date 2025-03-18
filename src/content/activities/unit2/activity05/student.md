## Solución a la quinta actividad
### Código que lea un carácter enviado por el puerto serial y lo muestre en la pantalla LED:
#### Código de micro:bit Phyton editor:
``` Python
from microbit import *

# Inicializa la comunicación serial con baudrate 115200
uart.init(baudrate=115200)

while True:
    # Verifica si hay datos en el buffer serial
    if uart.any():
        data = uart.read(1)  # Lee un byte
        if data:
            # Convierte el byte recibido a carácter
            received_char = chr(data[0])
            # Muestra el carácter desplazándolo en la pantalla LED
            display.scroll(received_char)

```
#### Código de p5.js web Editor:
``` js
let port;
let writer;

function setup() {
  createCanvas(400, 400);
  
  // Botón para conectar el puerto serial
  let connectButton = createButton('Conectar');
  connectButton.position(20, 20);
  connectButton.mousePressed(connectSerial);
  
  // Campo de entrada para enviar un carácter (máximo 1 carácter)
  let charInput = createInput('');
  charInput.position(20, 60);
  charInput.attribute('maxlength', '1');
  
  // Cada vez que se escribe en el campo se envía el carácter
  charInput.input(() => {
    let char = charInput.value();
    if (writer && char) {
      writer.write(new TextEncoder().encode(char));
    }
  });
}

async function connectSerial() {
  // Solicita acceso al puerto serial disponible
  port = await navigator.serial.requestPort();
  await port.open({ baudRate: 115200 });
  writer = port.writable.getWriter();
  console.log('Puerto serial conectado');
}
```
