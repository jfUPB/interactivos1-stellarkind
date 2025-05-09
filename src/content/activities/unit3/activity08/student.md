## Solución a la octava actividad
### Control remoto desde el micro:bit
**Código Actualizado del Micro:bit (Python):**
``` python
from microbit import *
import utime

uart.init(baudrate=115200)
display.show(Image.GIRAFFE)

while True:
    if button_a.was_pressed():
        uart.write('A')
        display.show("A")
        
    if button_b.was_pressed():
        uart.write('B')  
        display.show("B")

    if accelerometer.was_gesture('shake'):
        uart.write('S')  
        display.show("S")

    if pin_logo.is_touched():
        uart.write('T')
        display.show("T")

```

**Código Actualizado de p5.js (JavaScript):**
``` js
let countdown = 20;
let state = "CONFIG"; //Define el estado inicial de la bomba como CONFIG (modo configuración).
let lastTick;
let shakeCounter = 0; //Contador para detectar el 'shake' del mouse y evitar detecciones accidentales
const shakeThreshold = 10; //Define cuántos movimientos son necesarios para que el 'shake' sea válido.
let lastMouseX, lastMouseY;
let cooldownActive = false;
let shakeEvent = false;
let leftEvent = false;
let rightEvent = false;
let centerEvent = false;

let password = [];
let deactivation_sequence = ["r", "l", "r", "s"]; //Secuencia correcta para desactivar la bomba durante la cuenta regresiva.
let current_step = 0;

// --- Variables para la comunicación Serial ---
let port;
let connectBtn;

function setup() {
  createCanvas(400, 400);
  textAlign(CENTER, CENTER);
  textSize(40);
  lastTick = millis();
  lastMouseX = mouseX;
  lastMouseY = mouseY;

  // --- Create the serial port and the connect button ---
  port = createSerial(); // Create a new serial port object
  connectBtn = createButton("Connect to micro:bit"); // Create the button
  connectBtn.position(20, 20); // Position the button (adjust as needed)
  connectBtn.mousePressed(connectBtnClick); // Assign the click handler function
}

function draw() {
  //Función principal que actualiza el estado de la bomba y su representación visual en la pantalla.

  switch (state) {
    case "CONFIG":
      configState();
      //print("CONFIG");
      break;
    case "COUNTDOWN":
      countDownState();
      //print("COUNTDOWN");
      break;

    case "EXPLODED":
      explodeState();
      break;
  }

  if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
  } else {
    connectBtn.html("Disconnect");
    
    if (port.availableBytes() > 0) {
      let dataRx = port.read(1);
      
      if (dataRx == "A") {
        leftEvent = true;
      } else if (dataRx == "B") {
        rightEvent = true;
      } else if (dataRx == "S") {
        shakeEvent = true;
      }else if (dataRx == "T") {
        centerEvent = true;
      }
      
    }
  }
}

function mousePressed() {
  if (mouseButton === LEFT) {
    leftEvent = true;
    console.log("LEFT");
  } else if (mouseButton === RIGHT) {
    rightEvent = true;
    console.log("RIGHT");
  } else if (mouseButton === CENTER) {
    centerEvent = true;
    console.log("CENTER");
  }
}

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
    console.log("Attempting to connect...");
  } else {
    port.close();
    console.log("Disconnected.");
  }
}

function configState() {
  // Manejo la UI
  background(50);
  fill(100);
  rect(width / 2 - 75, height / 2 + 60, 150, 50, 10);
  fill(0, 255, 0); // Verde en CONFIG
  ellipse(width / 2, height / 2 + 85, 30, 30);
  fill(0, 255, 0);
  text("CONFIG MODE", width / 2, 100);
  fill(255);
  text(countdown, width / 2, height / 2);

  // Leo los eventos y ENVIO SERIAL si el puerto esta abierto
  if (leftEvent === true && countdown > 10) {
    leftEvent = false;
    countdown--;
    if (port.opened()) {
      port.write("A");
      console.log("Sent 'A'");
    }
  }

  if (rightEvent === true && countdown < 60) {
    rightEvent = false;
    countdown++;
    if (port.opened()) {
      port.write("B");
      console.log("Sent 'B'");
    }
  }

  if (shakeEvent == true) {
    shakeEvent = false;
    // Acciones correspondientes al evento
    console.log("Change to CountDown");

    if (port.opened()) {
      port.write("S");
      console.log("Sent 'S'");
    }
  
    // Condiciones iniciales que necesita COUNTDOWN
    lastTick = millis();
    state = "COUNTDOWN";
  }
}

function countDownState() {
  // Manejo la UI
  background(50);
  fill(100);
  rect(width / 2 - 75, height / 2 + 60, 150, 50, 10);
  fill(255, 255, 0); // Amarillo en COUNTDOWN
  ellipse(width / 2, height / 2 + 85, 30, 30);

  fill(255, 165, 0);
  text("ARMED!", width / 2, 100);
  fill(255);
  text(countdown, width / 2, height / 2);

  if (millis() - lastTick >= 1000 && countdown > 0) {
    countdown -= 1;
    lastTick = millis();
    if (countdown <= 0) {
      state = "EXPLODED";
    }
  }

  // Leo los eventos y ENVIO SERIAL si el puerto esta abierto

  // Eventos de teclado (Mouse clicks act as 'keys' here)
  if (leftEvent === true) {
    leftEvent = false;
    password[current_step] = "l";
    current_step++;
    if (port.opened()) {
      port.write("A");
      console.log("Sent 'A'");
    }
  }

  if (rightEvent === true) {
    rightEvent = false;
    password[current_step] = "r";
    current_step++;
    if (port.opened()) {
      port.write("B");
      console.log("Sent 'B'");
    }
  }

  if (shakeEvent === true) {
    shakeEvent = false;
    password[current_step] = "s";
    current_step++;
    if (port.opened()) {
      port.write("S");
      console.log("Sent 'S'");
    }
  }


  if (password.length === deactivation_sequence.length) {
    console.log("CLave completa... estara bien?");
    console.log(password);
    // Verificar si el contenido de ambos arreglos es igual
    // Si son iguales desactivo y voy al estado estado siguiente
    // No puedo olvidar las condiciones iniciales para ese estado
    const result = password.every(
      (val, index) => val === deactivation_sequence[index]
    );
    if (result === true) {
      password = [];
      current_step = 0;
      cooldownActive = true;
      setTimeout(() => {
        cooldownActive = false;
      }, 2000);
      countdown = 20;

      state = "CONFIG";
      console.log("Bomba desactivada");
    } else {
      password = [];
      current_step = 0;
      console.log("Incorrect sequence.");
    }
  }
}

function explodeState() {
  // Manejo la UI
  background(50);
  if (frameCount % 20 < 10) {
    background(255, 0, 0); // Rojo
  } else {
    background(0); // Negro
  }
  textAlign(CENTER, CENTER);
  textSize(100);
  if (frameCount % 20 < 10) {
    fill(0); // Texto Negro si el fondo es rojo
  } else {
    fill(255); // Texto Blanco si el fondo es negro
  }
  text("💀", width / 2, height / 2 - 30); // Dibuja la calavera en el centro
  textSize(40);
  text("BOOM!", width / 2, height / 2 + 20);

  fill(100);
  rect(width / 2 - 75, height / 2 + 60, 150, 50, 10);
  fill(255, 0, 0); // Rojo en EXPLODED
  ellipse(width / 2, height / 2 + 85, 30, 30);

  if (centerEvent === true) {
    centerEvent = false;
    console.log("TOUCH - RESET");

    if (port.opened()) {
      port.write("T");
      console.log("Sent 'T'");
    }

    state = "CONFIG";
    countdown = 20;
    password = [];
    current_step = 0;
    shakeCounter = 0;
    leftEvent = false;
    rightEvent = false;
    cooldownActive = true;
    setTimeout(() => {
      cooldownActive = false;
    }, 2000);
  }

  if (leftEvent === true) leftEvent = false;
  if (rightEvent === true) rightEvent = false;
}

function mouseMoved() {
  let movement = dist(mouseX, mouseY, lastMouseX, lastMouseY);
  if (state !== "EXPLODED" && movement > 50) {
    shakeCounter++;
    if (shakeCounter >= shakeThreshold) {
      shakeEvent = true;
      shakeCounter = 0; // Reset the counter after a shake is detected
      console.log("SHAKE Detected!");
    }
  } else if (state === "EXPLODED") {
    shakeCounter = 0;
  }

  lastMouseX = mouseX;
  lastMouseY = mouseY;
}

```
