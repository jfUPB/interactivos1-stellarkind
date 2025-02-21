## Solución a la quinta actividad
### Código que lea un carácter enviado por el puerto serial y lo muestre en la pantalla LED:
#### Código de micro:bit Phyton editor:
``` Python
from microbit import *
uart.init(baudrate=115200)

while True:
    if uart.any():  # Si hay datos en el puerto serial
        data = uart.read(1)  # Leer un solo carácter
        if data:
            char = chr(data[0])  # Convertir de byte a texto
            display.show(char)  # Mostrar el carácter en la pantalla LED
```
#### Código de p5.js web Editor:
``` js
let port;
let connectBtn;
let sendBtn;

function setup() {
    createCanvas(400, 400); // Crea un lienzo de 400x400 píxeles
    background(220); // Establece un fondo gris claro

    port = createSerial(); // Inicializa la conexión serial

    connectBtn = createButton('Connect to micro:bit'); // Crea un botón para conectar el micro:bit
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick); // Asigna una función al hacer clic

    sendBtn = createButton('Send A'); // Crea un botón para enviar el carácter 'A'
    sendBtn.position(220, 300);
    sendBtn.mousePressed(() => sendCommand('A')); // Asigna la función para enviar el comando
}

function draw() {
    if (!port.opened()) {
        connectBtn.html('Connect to micro:bit'); // Cambia el texto del botón si no está conectado
    } else {
        connectBtn.html('Disconnect'); // Cambia el texto del botón si está conectado
    }
}

function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200); // Abre el puerto serial con la configuración de MicroPython
    } else {
        port.close(); // Cierra el puerto si ya está abierto
    }
}

function sendCommand(command) {
    port.write(command); // Envía el comando especificado por el puerto serial
    console.log("Enviado:", command); // Muestra en la consola el comando enviado
}
```
