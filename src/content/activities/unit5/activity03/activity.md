#### Caso de estudio: p5.js

🎯 **Enunciado**: ahora vamos a modificar el código de p5.js para soportar 
la lectura de datos en formato binario.

Te voy a proponer que temporalmente envíes los mismos datos desde el micro:bit. La 
idea es que puedas saber exactamente qué datos estás enviando y de esta manera 
verificar que el código de p5.js está funcionando correctamente.

Para esto, cambia el código del micro:bit por este:

``` py
# Imports go at the top
from microbit import *
import struct
uart.init(115200)
display.set_pixel(0,0,9)

while True:
    """
    xValue = accelerometer.get_x()
    yValue = accelerometer.get_y()
    aState = button_a.is_pressed()
    bState = button_b.is_pressed()
    """
    xValue = 500
    yValue = 524
    aState = True
    bState = False
    data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
    uart.write(data)
    sleep(100) # Envia datos a 10 Hz
```

Te recuerdo: si los valores de xValue y yValue son enteros cortos de 2 bytes,
aState y bState son enteros sin signo de 1 byte, entonces el tamaño total del paquete
es de 2 + 2 + 1 + 1 = 6 bytes. Por ejemplo, en este caso:

``` py
xValue = 500
yValue = 524
aState = True
bState = False
```
El paquete que se enviaría sería:

``` py
01 f4 02 0c 01 00
```

Los primeros dos bytes son el valor de xValue (500) en big-endian y en hexadecimal
(01 f4), los siguientes dos bytes son el valor de yValue (524) en big-endian y en
hexadecimal (02 0c), y los últimos dos bytes son el valor de aState (True) y bState (False)
en hexadecimal (01 00).

En la unidad anterior era necesario separar los valores y marcar el fin del paquete
con un salto de línea. Ahora no es necesario, ya que el tamaño del paquete es fijo
y no se necesita un delimitador. En este caso, el paquete es de 6 bytes y no se 
necesita un salto de línea para indicar el final del paquete. 

🧐🧪✍️ Explica por qué en la unidad anterior teníamos que enviar la información 
delimitada y además marcada con un salto de línea y ahora no es necesario.

Con lo anterior en mente, ahora vas a modificar el código de p5.js para leer 
los datos en formato binario. Sin embargo, al igual que con el código del micro:bit, 
te pediré que primero verifiquemos si los datos se están enviando correctamente.

 Cambia el código de p5.js para que se vea así:

``` js
let c;
let lineModuleSize = 0;
let angle = 0;
let angleSpeed = 1;
const lineModule = [];
let lineModuleIndex = 0;
let clickPosX = 0;
let clickPosY = 0;

function preload() {
  lineModule[1] = loadImage("02.svg");
  lineModule[2] = loadImage("03.svg");
  lineModule[3] = loadImage("04.svg");
  lineModule[4] = loadImage("05.svg");
}

let port;
let connectBtn;
let microBitConnected = false;

const STATES = {
  WAIT_MICROBIT_CONNECTION: "WAITMICROBIT_CONNECTION",
  RUNNING: "RUNNING",
};
let appState = STATES.WAIT_MICROBIT_CONNECTION;
let microBitX = 0;
let microBitY = 0;
let microBitAState = false;
let microBitBState = false;
let prevmicroBitAState = false;
let prevmicroBitBState = false;

function setup() {
  createCanvas(windowWidth, windowHeight);
  background(255);

  port = createSerial();
  connectBtn = createButton("Connect to micro:bit");
  connectBtn.position(0, 0);
  connectBtn.mousePressed(connectBtnClick);
}

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
  } else {
    port.close();
  }
}

function updateButtonStates(newAState, newBState) {
  // Generar eventos de keypressed
  if (newAState === true && prevmicroBitAState === false) {
    // create a new random color and line length
    lineModuleSize = random(50, 160);
    // remember click position
    clickPosX = microBitX;
    clickPosY = microBitY;
    print("A pressed");
  }
  // Generar eventos de key released
  if (newBState === false && prevmicroBitBState === true) {
    c = color(random(255), random(255), random(255), random(80, 100));
    print("B released");
  }

  prevmicroBitAState = newAState;
  prevmicroBitBState = newBState;
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

function draw() {
  //******************************************
  if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
    microBitConnected = false;
  } else {
    microBitConnected = true;
    connectBtn.html("Disconnect");

    if (port.availableBytes() >= 6) {
      let data = port.readBytes(6);
      if (data) {
        const buffer = new Uint8Array(data).buffer;
        const view = new DataView(buffer);
        microBitX = view.getInt16(0);
        microBitY = view.getInt16(2);
        microBitAState = view.getUint8(4) === 1;
        microBitBState = view.getUint8(5) === 1;
        updateButtonStates(microBitAState, microBitBState);
        
        print(`microBitX: ${microBitX} microBitY: ${microBitY} microBitAState: ${microBitAState} microBitBState: ${microBitBState} \n` );
        
        /*
        microBitX = int(values[0]) + windowWidth / 2;
        microBitY = int(values[1]) + windowHeight / 2;
        microBitAState = values[2].toLowerCase() === "true";
        microBitBState = values[3].toLowerCase() === "true";
              
        updateButtonStates(microBitAState, microBitBState);
        */
        
      }
    }
  }
  //*******************************************

  switch (appState) {
    case STATES.WAIT_MICROBIT_CONNECTION:
      // No puede comenzar a dibujar hasta que no se conecte el microbit
      // evento 1:
      if (microBitConnected === true) {
        // Preparo todo para el estado en el próximo frame
        print("Microbit ready to draw");
        strokeWeight(0.75);
        c = color(181, 157, 0);
        noCursor();
        appState = STATES.RUNNING;
      }

      break;

    case STATES.RUNNING:
      // EVENTO: estado de conexión del microbit
      if (microBitConnected === false) {
        print("Waiting microbit connection");
        cursor();
        appState = STATES.WAIT_MICROBIT_CONNECTION;
      }

      //EVENTO: recepción de datos seriales del micro:bit

      if (microBitAState === true) {
        let x = microBitX;
        let y = microBitY;

        if (keyIsPressed && keyCode === SHIFT) {
          if (abs(clickPosX - x) > abs(clickPosY - y)) {
            y = clickPosY;
          } else {
            x = clickPosX;
          }
        }

        push();
        translate(x, y);
        rotate(radians(angle));
        if (lineModuleIndex != 0) {
          tint(c);
          image(
            lineModule[lineModuleIndex],
            0,
            0,
            lineModuleSize,
            lineModuleSize
          );
        } else {
          stroke(c);
          line(0, 0, lineModuleSize, lineModuleSize);
        }
        angle += angleSpeed;
        pop();
      }

      break;
  }
}

function keyPressed() {
  if (keyCode === UP_ARROW) lineModuleSize += 5;
  if (keyCode === DOWN_ARROW) lineModuleSize -= 5;
  if (keyCode === LEFT_ARROW) angleSpeed -= 0.5;
  if (keyCode === RIGHT_ARROW) angleSpeed += 0.5;
}

function keyReleased() {
  if (key === "s" || key === "S") {
    let ts =
      year() +
      nf(month(), 2) +
      nf(day(), 2) +
      "_" +
      nf(hour(), 2) +
      nf(minute(), 2) +
      nf(second(), 2);
    saveCanvas(ts, "png");
  }
  if (keyCode === DELETE || keyCode === BACKSPACE) background(255);

  // reverse direction and mirror angle
  if (key === "d" || key === "D") {
    angle += 180;
    angleSpeed *= -1;
  }

  // default colors from 1 to 4
  if (key === "1") c = color(181, 157, 0);
  if (key === "2") c = color(0, 130, 164);
  if (key === "3") c = color(87, 35, 129);
  if (key === "4") c = color(197, 0, 123);

  // load svg for line module
  if (key === "5") lineModuleIndex = 0;
  if (key === "6") lineModuleIndex = 1;
  if (key === "7") lineModuleIndex = 2;
  if (key === "8") lineModuleIndex = 3;
  if (key === "9") lineModuleIndex = 4;
}
```

Casi todo el código es el mismo que en la unidad anterior, pero esta 
vez nos vamos a concentrar solo en esta parte:

``` js
if (port.availableBytes() >= 6) {
    let data = port.readBytes(6);
    if (data) {
    const buffer = new Uint8Array(data).buffer;
    const view = new DataView(buffer);
    microBitX = view.getInt16(0);
    microBitY = view.getInt16(2);
    microBitAState = view.getUint8(4) === 1;
    microBitBState = view.getUint8(5) === 1;
    updateButtonStates(microBitAState, microBitBState);
    
    print(`microBitX: ${microBitX} microBitY: ${microBitY} microBitAState: ${microBitAState} microBitBState: ${microBitBState} \n` );
    
    /*
    microBitX = int(values[0]) + windowWidth / 2;
    microBitY = int(values[1]) + windowHeight / 2;
    microBitAState = values[2].toLowerCase() === "true";
    microBitBState = values[3].toLowerCase() === "true";
    updateButtonStates(microBitAState, microBitBState);
    */
    
    }
}
```

🧐🧪✍️ Compara el código de la unidad anterior con este. ¿Qué 
cambios ves?

Ahora te voy a pedir que ejecutes el código de p5.js muchas veces y que 
estés muy atento a la consola. Lo que haremos es a tratar de reproducir un error 
que tiene este código. El error es de sincronización y se produce cuando 
los 6 bytes que lee el código de p5.js no corresponden a los mismos 6 bytes 
que envía el micro:bit. 

Te voy mostrar por ejemplo un resultado que obtuve al ejecutar el código 
de p5.js:

``` js

Connected to serial port 
A pressed 
microBitX: 500 microBitY: 524 microBitAState: true microBitBState: false 

Microbit ready to draw 
92 microBitX: 500 microBitY: 524 microBitAState: true microBitBState: false 
 
microBitX: 500 microBitY: 513 microBitAState: false microBitBState: false 
 
222 microBitX: 3073 microBitY: 1 microBitAState: false microBitBState: false 
```

🧐🧪✍️ ¿Qué ves en la consola? ¿Por qué crees que se produce este error? 

Para solucionar este tipo de problemas, es usual que los comunicaciones seriales 
implementen una estrategia de sincronización. La estrategia que vamos a usar 
se denomina ``framing`` y consiste en enviar un byte de inicio y un byte de fin 
del paquete. 

**¿Por qué necesitamos framing?**

Cuando se envían datos a través de una comunicación serial, los bytes pueden llegar en fragmentos 
arbitrarios y sin respetar los límites de los paquetes. Esto significa que, sin un 
mecanismo que delimite el inicio y el fin de cada paquete, el receptor puede comenzar 
a leer a mitad de un paquete o mezclar bytes de dos paquetes consecutivos. Este 
desalineamiento puede producir interpretaciones erróneas (por ejemplo, obtener valores 
como ``microBitY: 513`` en lugar de 524) y dificultar la detección de errores. Con el 
framing se asegura que:

- Sincronización: el receptor identifica claramente dónde comienza un paquete 
(por ejemplo, usando un byte específico como header o byte inicial).

- Integridad: se puede incluir un checksum o CRC para verificar que los datos 
recibidos sean correctos.

- Robustez: incluso si la comunicación se fragmenta o se reciben datos residuales, 
el receptor puede descartar bytes hasta encontrar un paquete completo y correcto.

Para implementar esta estrategia será necesario modificar el código 
del micro:bit y el código de p5.js.

En e caso del micro:bit se enviará un paquete de 8 bytes:

```
Byte 0: Header (0xAA)
Bytes 1-6: Datos (dos enteros de 16 bits y dos bytes para estados)
Byte 7: Checksum (suma de los 6 bytes de datos módulo 256)
```

De nuevo, el código del micro:bit quedaría así:

``` py
from microbit import *
import struct

uart.init(115200)
display.set_pixel(0, 0, 9)

while True:
    xValue = 500
    yValue = 524
    aState = True
    bState = False
    # Empaqueta los datos: 2 enteros (16 bits) y 2 bytes para estados
    data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
    # Calcula un checksum simple: suma de los bytes de data módulo 256
    checksum = sum(data) % 256
    # Crea el paquete con header, datos y checksum
    packet = b'\xAA' + data + bytes([checksum])
    uart.write(packet)
    sleep(100)  # Envía datos a 10 Hz
```

Ahora, el código de p5.js quedaría así:

``` js
let serialBuffer = []; // Buffer para almacenar bytes recibidos

let c;
let lineModuleSize = 0;
let angle = 0;
let angleSpeed = 1;
const lineModule = [];
let lineModuleIndex = 0;
let clickPosX = 0;
let clickPosY = 0;

function preload() {
  lineModule[1] = loadImage("02.svg");
  lineModule[2] = loadImage("03.svg");
  lineModule[3] = loadImage("04.svg");
  lineModule[4] = loadImage("05.svg");
}

let port;
let connectBtn;
let microBitConnected = false;

const STATES = {
  WAIT_MICROBIT_CONNECTION: "WAITMICROBIT_CONNECTION",
  RUNNING: "RUNNING",
};
let appState = STATES.WAIT_MICROBIT_CONNECTION;
let microBitX = 0;
let microBitY = 0;
let microBitAState = false;
let microBitBState = false;
let prevmicroBitAState = false;
let prevmicroBitBState = false;

function setup() {
  createCanvas(windowWidth, windowHeight);
  background(255);

  port = createSerial();
  connectBtn = createButton("Connect to micro:bit");
  connectBtn.position(0, 0);
  connectBtn.mousePressed(connectBtnClick);
}

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
  } else {
    port.close();
  }
}

function updateButtonStates(newAState, newBState) {
  // Generar eventos de keypressed
  if (newAState === true && prevmicroBitAState === false) {
    // create a new random color and line length
    lineModuleSize = random(50, 160);
    // remember click position
    clickPosX = microBitX;
    clickPosY = microBitY;
    print("A pressed");
  }
  // Generar eventos de key released
  if (newBState === false && prevmicroBitBState === true) {
    c = color(random(255), random(255), random(255), random(80, 100));
    print("B released");
  }

  prevmicroBitAState = newAState;
  prevmicroBitBState = newBState;
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

function readSerialData() {
  // Acumula los bytes recibidos en el buffer
  let available = port.availableBytes();
  if (available > 0) {
    let newData = port.readBytes(available);
    serialBuffer = serialBuffer.concat(newData);
  }

  // Procesa el buffer mientras tenga al menos 8 bytes (tamaño de un paquete)
  while (serialBuffer.length >= 8) {
    // Busca el header (0xAA)
    if (serialBuffer[0] !== 0xaa) {
      serialBuffer.shift(); // Descarta bytes hasta encontrar el header
      continue;
    }

    // Si hay menos de 8 bytes, espera a que llegue el paquete completo
    if (serialBuffer.length < 8) break;

    // Extrae los 8 bytes del paquete
    let packet = serialBuffer.slice(0, 8);
    serialBuffer.splice(0, 8); // Elimina el paquete procesado del buffer

    // Separa datos y checksum
    let dataBytes = packet.slice(1, 7);
    let receivedChecksum = packet[7];
    // Calcula el checksum sumando los datos y aplicando módulo 256
    let computedChecksum = dataBytes.reduce((acc, val) => acc + val, 0) % 256;

    if (computedChecksum !== receivedChecksum) {
      console.log("Checksum error in packet");
      continue; // Descarta el paquete si el checksum no es válido
    }

    // Si el paquete es válido, extrae los valores
    let buffer = new Uint8Array(dataBytes).buffer;
    let view = new DataView(buffer);
    microBitX = view.getInt16(0);
    microBitY = view.getInt16(2);
    microBitAState = view.getUint8(4) === 1;
    microBitBState = view.getUint8(5) === 1;
    updateButtonStates(microBitAState, microBitBState);

    console.log(
      `microBitX: ${microBitX} microBitY: ${microBitY} microBitAState: ${microBitAState} microBitBState: ${microBitBState}`
    );
  }
}

function draw() {
  //******************************************
  if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
    microBitConnected = false;
  } else {
    microBitConnected = true;
    connectBtn.html("Disconnect");
  }

  //*******************************************

  switch (appState) {
    case STATES.WAIT_MICROBIT_CONNECTION:
      // No puede comenzar a dibujar hasta que no se conecte el microbit
      // evento 1:
      if (microBitConnected === true) {
        // Preparo todo para el estado en el próximo frame
        print("Microbit ready to draw");
        strokeWeight(0.75);
        c = color(181, 157, 0);
        noCursor();
        port.clear();
        prevmicroBitAState = false;
        prevmicroBitBState = false;
        appState = STATES.RUNNING;
      }

      break;

    case STATES.RUNNING:
      // EVENTO: estado de conexión del microbit
      if (microBitConnected === false) {
        print("Waiting microbit connection");
        cursor();
        appState = STATES.WAIT_MICROBIT_CONNECTION;
        break;
      }

      //EVENTO: recepción de datos seriales del micro:bit

      readSerialData();

      if (microBitAState === true) {
        let x = microBitX;
        let y = microBitY;

        if (keyIsPressed && keyCode === SHIFT) {
          if (abs(clickPosX - x) > abs(clickPosY - y)) {
            y = clickPosY;
          } else {
            x = clickPosX;
          }
        }

        push();
        translate(x, y);
        rotate(radians(angle));
        if (lineModuleIndex != 0) {
          tint(c);
          image(
            lineModule[lineModuleIndex],
            0,
            0,
            lineModuleSize,
            lineModuleSize
          );
        } else {
          stroke(c);
          line(0, 0, lineModuleSize, lineModuleSize);
        }
        angle += angleSpeed;
        pop();
      }

      break;
  }
}

function keyPressed() {
  if (keyCode === UP_ARROW) lineModuleSize += 5;
  if (keyCode === DOWN_ARROW) lineModuleSize -= 5;
  if (keyCode === LEFT_ARROW) angleSpeed -= 0.5;
  if (keyCode === RIGHT_ARROW) angleSpeed += 0.5;
}

function keyReleased() {
  if (key === "s" || key === "S") {
    let ts =
      year() +
      nf(month(), 2) +
      nf(day(), 2) +
      "_" +
      nf(hour(), 2) +
      nf(minute(), 2) +
      nf(second(), 2);
    saveCanvas(ts, "png");
  }
  if (keyCode === DELETE || keyCode === BACKSPACE) background(255);

  // reverse direction and mirror angle
  if (key === "d" || key === "D") {
    angle += 180;
    angleSpeed *= -1;
  }

  // default colors from 1 to 4
  if (key === "1") c = color(181, 157, 0);
  if (key === "2") c = color(0, 130, 164);
  if (key === "3") c = color(87, 35, 129);
  if (key === "4") c = color(197, 0, 123);

  // load svg for line module
  if (key === "5") lineModuleIndex = 0;
  if (key === "6") lineModuleIndex = 1;
  if (key === "7") lineModuleIndex = 2;
  if (key === "8") lineModuleIndex = 3;
  if (key === "9") lineModuleIndex = 4;
}

```

🧐🧪✍️ Analiza el código, observa los cambios. Ejecuta y luego observa 
la consola. ¿Qué ves?

La versión final de los programas de micro:bit y p5.js
son las siguientes:

``` py
from microbit import *
import struct

uart.init(115200)
display.set_pixel(0, 0, 9)

while True:
    xValue = accelerometer.get_x()
    yValue = accelerometer.get_y()
    aState = button_a.is_pressed()
    bState = button_b.is_pressed()
    # Empaqueta los datos: 2 enteros (16 bits) y 2 bytes para estados
    data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
    # Calcula un checksum simple: suma de los bytes de data módulo 256
    checksum = sum(data) % 256
    # Crea el paquete con header, datos y checksum
    packet = b'\xAA' + data + bytes([checksum])
    uart.write(packet)
    sleep(100)  # Envía datos a 10 Hz
```

``` js
let serialBuffer = []; // Buffer para almacenar bytes recibidos

let c;
let lineModuleSize = 0;
let angle = 0;
let angleSpeed = 1;
const lineModule = [];
let lineModuleIndex = 0;
let clickPosX = 0;
let clickPosY = 0;

function preload() {
  lineModule[1] = loadImage("02.svg");
  lineModule[2] = loadImage("03.svg");
  lineModule[3] = loadImage("04.svg");
  lineModule[4] = loadImage("05.svg");
}

let port;
let connectBtn;
let microBitConnected = false;

const STATES = {
  WAIT_MICROBIT_CONNECTION: "WAITMICROBIT_CONNECTION",
  RUNNING: "RUNNING",
};
let appState = STATES.WAIT_MICROBIT_CONNECTION;
let microBitX = 0;
let microBitY = 0;
let microBitAState = false;
let microBitBState = false;
let prevmicroBitAState = false;
let prevmicroBitBState = false;

function setup() {
  createCanvas(windowWidth, windowHeight);
  background(255);

  port = createSerial();
  connectBtn = createButton("Connect to micro:bit");
  connectBtn.position(0, 0);
  connectBtn.mousePressed(connectBtnClick);
}

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
  } else {
    port.close();
  }
}

function updateButtonStates(newAState, newBState) {
  // Generar eventos de keypressed
  if (newAState === true && prevmicroBitAState === false) {
    // create a new random color and line length
    lineModuleSize = random(50, 160);
    // remember click position
    clickPosX = microBitX;
    clickPosY = microBitY;
    print("A pressed");
  }
  // Generar eventos de key released
  if (newBState === false && prevmicroBitBState === true) {
    c = color(random(255), random(255), random(255), random(80, 100));
    print("B released");
  }

  prevmicroBitAState = newAState;
  prevmicroBitBState = newBState;
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

function readSerialData() {
  // Acumula los bytes recibidos en el buffer
  let available = port.availableBytes();
  if (available > 0) {
    let newData = port.readBytes(available);
    serialBuffer = serialBuffer.concat(newData);
  }

  // Procesa el buffer mientras tenga al menos 8 bytes (tamaño de un paquete)
  while (serialBuffer.length >= 8) {
    // Busca el header (0xAA)
    if (serialBuffer[0] !== 0xaa) {
      serialBuffer.shift(); // Descarta bytes hasta encontrar el header
      continue;
    }

    // Si hay menos de 8 bytes, espera a que llegue el paquete completo
    if (serialBuffer.length < 8) break;

    // Extrae los 8 bytes del paquete
    let packet = serialBuffer.slice(0, 8);
    serialBuffer.splice(0, 8); // Elimina el paquete procesado del buffer

    // Separa datos y checksum
    let dataBytes = packet.slice(1, 7);
    let receivedChecksum = packet[7];
    // Calcula el checksum sumando los datos y aplicando módulo 256
    let computedChecksum = dataBytes.reduce((acc, val) => acc + val, 0) % 256;

    if (computedChecksum !== receivedChecksum) {
      console.log("Checksum error in packet");
      continue; // Descarta el paquete si el checksum no es válido
    }

    // Si el paquete es válido, extrae los valores
    let buffer = new Uint8Array(dataBytes).buffer;
    let view = new DataView(buffer);
    microBitX = view.getInt16(0) + windowWidth / 2;
    microBitY = view.getInt16(2) + windowHeight / 2;
    microBitAState = view.getUint8(4) === 1;
    microBitBState = view.getUint8(5) === 1;
    updateButtonStates(microBitAState, microBitBState);

  }
}

function draw() {
  //******************************************
  if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
    microBitConnected = false;
  } else {
    microBitConnected = true;
    connectBtn.html("Disconnect");
  }

  //*******************************************

  switch (appState) {
    case STATES.WAIT_MICROBIT_CONNECTION:
      // No puede comenzar a dibujar hasta que no se conecte el microbit
      // evento 1:
      if (microBitConnected === true) {
        // Preparo todo para el estado en el próximo frame
        print("Microbit ready to draw");
        strokeWeight(0.75);
        c = color(181, 157, 0);
        noCursor();
        port.clear();
        prevmicroBitAState = false;
        prevmicroBitBState = false;
        appState = STATES.RUNNING;
      }

      break;

    case STATES.RUNNING:
      // EVENTO: estado de conexión del microbit
      if (microBitConnected === false) {
        print("Waiting microbit connection");
        cursor();
        appState = STATES.WAIT_MICROBIT_CONNECTION;
        break;
      }

      //EVENTO: recepción de datos seriales del micro:bit

      readSerialData();

      if (microBitAState === true) {
        let x = microBitX;
        let y = microBitY;

        if (keyIsPressed && keyCode === SHIFT) {
          if (abs(clickPosX - x) > abs(clickPosY - y)) {
            y = clickPosY;
          } else {
            x = clickPosX;
          }
        }

        push();
        translate(x, y);
        rotate(radians(angle));
        if (lineModuleIndex != 0) {
          tint(c);
          image(
            lineModule[lineModuleIndex],
            0,
            0,
            lineModuleSize,
            lineModuleSize
          );
        } else {
          stroke(c);
          line(0, 0, lineModuleSize, lineModuleSize);
        }
        angle += angleSpeed;
        pop();
      }

      break;
  }
}

function keyPressed() {
  if (keyCode === UP_ARROW) lineModuleSize += 5;
  if (keyCode === DOWN_ARROW) lineModuleSize -= 5;
  if (keyCode === LEFT_ARROW) angleSpeed -= 0.5;
  if (keyCode === RIGHT_ARROW) angleSpeed += 0.5;
}

function keyReleased() {
  if (key === "s" || key === "S") {
    let ts =
      year() +
      nf(month(), 2) +
      nf(day(), 2) +
      "_" +
      nf(hour(), 2) +
      nf(minute(), 2) +
      nf(second(), 2);
    saveCanvas(ts, "png");
  }
  if (keyCode === DELETE || keyCode === BACKSPACE) background(255);

  // reverse direction and mirror angle
  if (key === "d" || key === "D") {
    angle += 180;
    angleSpeed *= -1;
  }

  // default colors from 1 to 4
  if (key === "1") c = color(181, 157, 0);
  if (key === "2") c = color(0, 130, 164);
  if (key === "3") c = color(87, 35, 129);
  if (key === "4") c = color(197, 0, 123);

  // load svg for line module
  if (key === "5") lineModuleIndex = 0;
  if (key === "6") lineModuleIndex = 1;
  if (key === "7") lineModuleIndex = 2;
  if (key === "8") lineModuleIndex = 3;
  if (key === "9") lineModuleIndex = 4;
}
```

🧐🧪✍️ ¿Qué cambios tienen los programas y ¿Qué puedes observar en la consola del 
editor de p5.js?


📤 **Entrega**: reporta en la bitácora en todos los puntos que te marqué 
para analizar 🧐, experimentar 🧪 y reportar ✍️ tus hallazgos.
