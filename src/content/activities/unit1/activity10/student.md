## Solucion a la decima actividad 
### Programa en p5.js que muestre un cuadrado en la pantalla y un botón para que, al presionarlo, cambie el color del cuadrado en p5.js.
#### Código en p5.js:
``` js
let serial; 
let squareColor; 

function setup() {
  createCanvas(400, 400);
  squareColor = color(255, 0, 0); // Color inicial es rojo
  
  serial = new p5.SerialPort();
  serial.on('list', gotList);
  serial.on('data', gotData);
  
  // Abrir el puerto serial (modifica el nombre del puerto según tu sistema)
  // En Windows podría ser 'COM3'
  serial.open('COM3'); 
}

function gotList(portList) {
  // Imprime la lista de puertos en la consola para ayudarte a identificar el correcto
  for (let i = 0; i < portList.length; i++) {
    console.log(i + " " + portList[i]);
  }
}

function gotData() {
  // Lee la línea recibida del puerto serial
  let currentString = serial.readLine();
  currentString = currentString.trim();
  
  if (currentString) {
    console.log("Recibido: " + currentString);
    
    // Si se recibe el carácter "A", se cambia el color del cuadrado
    if (currentString === "A") {
      //genera un color aleatorio
      squareColor = color(random(255), random(255), random(255));
    }
  }
}
function draw()
{
  background(220);
  fill(squareColor);
  //DIbuja el cuadrado en el centro
  rect(150, 150, 100, 100);
}
```
#### Código en micro:bit Python:
``` phyton
from microbit import *
uart.init(baudrate=115200)

while True:
    if button_a.was_pressed():
        uart.write("A\n")
        display.show(Image.HEART)
    sleep(100)
```
Con estos programas al conectar el micro:bit al computador usando un cable USB y cargar en él el código se debería envíar un mensaje cuando se presiona un botón; 
al mismo tiempo, ejecutando el programa en p5.js, que abrir el puerto serial (por ejemplo, 'COM3' o el que sea) y quedaría a la espera de datos; cuando presione 
el botón del micro:bit, este enviaría un mensaje por el puerto serial que el programa en p5.js detecta, y en respuesta cambiarís el color del cuadrado en la pantalla, 
estableciendo así una comunicación directa y sencilla entre el micro:bit y la aplicación gráfica.
