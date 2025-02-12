## Solucion a la decima actividad 
### Programa en p5.js que muestre un cuadrado en la pantalla y un botón para que, al presionarlo, cambie el color del cuadrado en p5.js.
#### Código en p5.js:
``` js
let port;
let connectBtn;
let changeColorBtn;
let squareColor;

function setup() {
    createCanvas(400, 400);
    background(220);
    port = createSerial();

    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);

    changeColorBtn = createButton('Change Color');
    changeColorBtn.position(220, 300);
    changeColorBtn.mousePressed(() => sendCommand('C'));

    squareColor = color(255, 0, 0); // Color inicial: rojo
}

function draw() {
    background(220);
    fill(squareColor);
    rect(150, 150, 100, 100);

    if (port.availableBytes() > 0) {
        let dataRx = port.read(1);
        console.log("Recibido:", dataRx);
        
        if (dataRx == 'C') {
            squareColor = color(random(255), random(255), random(255));
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
#### Código en micro:bit Python:  
``` phyton
from microbit import *
uart.init(baudrate=115200)

while True:
    if button_a.was_pressed():
        uart.write('C')
    
    if uart.any():
        data = uart.read(1)
        if data:
            char = chr(data[0])
            if char == 'C':
                uart.write('C')
```
se ejecuta el código en p5.js, y se establece la conexión con el micro:bit desde la interfaz de p5.js. Una vez conectado, al presionar el botón A (el de la izquierda) en el micro:bit, este envía el carácter 'C' a p5.js, lo que provoca un cambio en el color del cuadrado en la pantalla. De manera similar, si se presiona el botón en la interfaz de p5.js, se envía el mismo carácter 'C' al micro:bit, que lo reenvía a p5.js, permitiendo así el cambio de color del cuadrado desde ambas entradas. Este mecanismo de comunicación serial bidireccional garantiza que tanto el botón físico en el micro:bit como el botón en la interfaz web puedan controlar la apariencia del cuadrado en pantalla de manera sincronizada.
