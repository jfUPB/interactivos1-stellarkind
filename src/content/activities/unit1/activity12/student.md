## Solución a la duodécima actividad  
### Programa en p5.js que permita seleccionar 4 imágenes diferentes en el micro:bit:  
Código para p5.js:
``` js
let port;
let connectBtn;
let buttons = [];

function setup() {
    createCanvas(400, 400);
    background(220);

    port = createSerial();

    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);

    let labels = ["Diamante", "Jirafa", "Feliz", "Mariposa"];
    let commands = ['D', 'G', 'H', 'B'];

    for (let i = 0; i < labels.length; i++) {
        let btn = createButton(labels[i]);
        btn.position(80 + i * 80, 350);
        btn.mousePressed(() => sendCommand(commands[i]));
        buttons.push(btn);
    }

}

function draw() {
    if (port.availableBytes() > 0) {
        let dataRx = port.read(1);
        console.log("Recibido:", dataRx);
    }

    if (!port.opened()) {
        connectBtn.html('Connect to micro:bit');
    } else {
        connectBtn.html('Disconnect');
    }
}

function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200);
    } else {
        port.close();
    }
}

function sendCommand(command) {
    port.write(command);
    console.log("Enviado:", command);
}
```

Código para micro:bit Phyton editor:
``` phyton
from microbit import *

uart.init(baudrate=115200)
display.show(Image.BUTTERFLY)

while True:
    if uart.any():
        data = uart.read(1)
        if data:
            char = chr(data[0])  
            if char == 'D':
                display.show(Image.DIAMOND)
            elif char == 'G':
                display.show(Image.GIRAFFE)
            elif char == 'H':
                display.show(Image.HAPPY)
            elif char == 'B':
                display.show(Image.BUTTERFLY)
```
Conectamos el micro:bit y este muestra una imagen inicial, en js creamos los botones que enviaran un imput string al microbit este en base a la letra que reciba 
muestra una imagen diferente en el microbit, un dimante, una jirafa, una carita feliz o una mariposa.
