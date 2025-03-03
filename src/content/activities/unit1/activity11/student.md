## Solución a la undécima actividad
### Programa en p5.js que muestre un círculo en la pantalla. Utiliza los botones A y B para controlar la posición en x del círculo en el canvas de p5.js.
#### Código para p5.js:
``` js
let port;
let connectBtn;
let resetBtn;
let circleX;

function setup() {
    createCanvas(400, 400);
    background(220);
    port = createSerial();
    
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);

    resetBtn = createButton('Reset Position');
    resetBtn.position(220, 300);
    resetBtn.mousePressed(() => sendCommand('C'));
    circleX = width / 2; // Dibuja en la posición inicial
}

function draw() {
    background(220);
    
    fill(50, 230, 220);
    ellipse(circleX, height / 2, 50, 50);

    if (port.availableBytes() > 0) {
        let dataRx = port.read(1);
        console.log("Recibido:", dataRx);
        
        if (dataRx == 'L') {
            circleX -= 10; // Mueve a la izquierda
        } else if (dataRx == 'R') {
            circleX += 10; // Mueve a la derecha
        } else if (dataRx == 'C') {
            circleX = width / 2; // Centra el círculo
        }
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
#### Código para micro:bit Phyton editor:
``` phyton
from microbit import *

uart.init(baudrate=115200)

while True:
    if button_a.was_pressed():
        uart.write('L')
    if button_b.was_pressed():
        uart.write('R')

    if uart.any():
        data = uart.read(1)
        if data:
            char = chr(data[0])
            if char == 'C':
                uart.write('C')

```

Cuando se presiona el botón A en el micro:bit, este manda la letra 'L' a p5.js, y el círculo en la pantalla se mueve a la izquierda. Si se presiona el botón B, envía 'R', y el círculo se mueve a la derecha. Además, en la interfaz de p5.js hay un botón llamado "Reset Position" que manda la letra 'C' al micro:bit, y este la reenvía de vuelta a p5.js para que el círculo regrese al centro. Básicamente, los botones físicos controlan el movimiento, y desde la pantalla también puedes reiniciar la posición, todo sincronizado entre el micro:bit y p5.js. 
