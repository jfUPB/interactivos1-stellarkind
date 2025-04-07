## Solución a la séptima actividad:
### Código sketch de bomba en p5.js:
```
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

function setup() {
  createCanvas(400, 400);
  textAlign(CENTER, CENTER);
  textSize(40);
  lastTick = millis();
  lastMouseX = mouseX;
  lastMouseY = mouseY;
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

  return;
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

  if (state === "CONFIG") {
  } else if (state === "COUNTDOWN") {
  } else if (state === "EXPLODED" && mouseButton === CENTER) {
    console.log("TOUCH - RESET");
    state = "CONFIG";
    countdown = 20;
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

  // Leo los eventos
  if (leftEvent === true && countdown > 10) {
    leftEvent = false;
    countdown--;
  }

  if (rightEvent === true && countdown < 60) {
    rightEvent = false;
    countdown++;
  }

  if (shakeEvent == true) {
    shakeEvent = false;
    // Acciones correspondientes al evento
    console.log("Change to CountDown");

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

  // Leo los eventos

  if (millis() - lastTick >= 1000 && countdown > 0) {
    countdown -= 1;
    lastTick = millis();
    if (countdown <= 0) {
      state = "EXPLODED";
    }
  }

  // Eventos de teclado
  if (leftEvent === true) {
    leftEvent = false;
    password[current_step] = "l";
    current_step++;
  }

  if (rightEvent === true) {
    rightEvent = false;
    password[current_step] = "r";
    current_step++;
  }

  if (shakeEvent === true) {
    shakeEvent = false;
    password[current_step] = "s";
    current_step++;
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

  // Leo los eventos
  leftEvent = false;
  rightEvent = false;
  current_step = 0;
  cooldownActive = true;
  shakeCounter = 0;
}

function mouseMoved() {
  let movement = dist(mouseX, mouseY, lastMouseX, lastMouseY);
  if (movement > 50) {
    shakeCounter++;
    //console.log("++");
    if (shakeCounter >= shakeThreshold + 10) {
      shakeEvent = true;
      shakeCounter = 0;
      console.log("SHAKE");
    }
  }
  lastMouseX = mouseX;
  lastMouseY = mouseY;
}

```
