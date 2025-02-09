## Solución a la undécima actividad
### Programa en p5.js que muestre un círculo en la pantalla. Utiliza los botones A y B para controlar la posición en x del círculo en el canvas de p5.js.
#### Código para p5.js:
``` js
let serial;
let circleX;
let moveStep = 10; // Paso de movimiento

function setup() {
  createCanvas(400, 400);
  circleX = width / 2; // Posición inicial en el centro
  serial = new p5.SerialPort();
  serial.on('list', gotList);
  serial.on('data', gotData);
  
  // Abrir el puerto serial (modifica 'COM3' según el sistema y entrada)
  serial.open('COM3');
}

function gotList(portList) {
  for (let i = 0; i < portList.length; i++) {
    console.log(i + " " + portList[i]);
  }
}

function gotData() {
  let currentString = serial.readLine();
  currentString = currentString.trim();
  
  if (currentString) {
    console.log("Recibido: " + currentString);
    if (currentString === "A") {
      circleX -= moveStep;
    } else if (currentString === "B") {
      circleX += moveStep; // Botón A mueve a la izquierda, Botón B mueve a la derecha
    }
  }
}

function draw() {
  background(220);
  fill(100, 150, 250);
  ellipse(circleX, height/2, 50, 50);
}
```
#### Código para micro:bit Phyton editor:
``` phyton
from microbit import *

# Inicializa la comunicación UART a 115200 baudios
uart.init(baudrate=115200)

while True:
    if button_a.was_pressed():
        uart.write("A\n")
        display.show(Image.ARROW_W)  # Opcional: muestra una flecha hacia la izquierda
    elif button_b.was_pressed():
        uart.write("B\n")
        display.show(Image.ARROW_E)  # Opcional: muestra una flecha hacia la derecha
    sleep(100)
```

Conecta el micro:bit al ordenador mediante un cable USB y se carga en él el código que envía un mensaje ("A" o "B") cuando se presionan los botones; luego, 
ejecuta el programa en p5.js, el cual abriría el puerto serial (por ejemplo, 'COM3') y espera recibir datos. Cuando el micro:bit envía "A", el programa en p5.js interpreta 
ese mensaje como una orden para mover el círculo hacia la izquierda (disminuyendo la coordenada x), y cuando se envía "B", el círculo debería moverse hacia la derecha 
(aumentando la coordenada x). De esta manera, cada botón del micro:bit se mapea a un comando específico que modifica la posición del círculo.
