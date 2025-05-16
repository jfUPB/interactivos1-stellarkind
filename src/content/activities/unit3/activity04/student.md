## Solución a la cuarta actividad
### Código de p5.js:
``` js
let serial;
let connectBtn, sendA, sendB, sendS, sendT;

function setup() {
  createCanvas(600, 400);
  background(220);
  serial = createSerial();
  
  let totalHeight = 30 + 20 + 30; // 80 px
  let startY = (height - totalHeight) / 2;
  let connectY = startY;
  let commandsY = connectY + 50;
  
  connectBtn = createButton('Connect to Bomb');
  connectBtn.style('background-color', 'yellow');
  connectBtn.style('width', '150px');
  connectBtn.position((width - 150) / 2, connectY);
  connectBtn.mousePressed(connectPort);
  
  sendA = createButton('Send A');
  sendA.style('width', '80px');
  sendA.position(120, commandsY);
  sendA.mousePressed(() => {
    serial.write('A');
  });
  sendB = createButton('Send B');
  sendB.style('width', '80px');
  sendB.position(220, commandsY);
  sendB.mousePressed(() => {
    serial.write('B');
  });
  sendS = createButton('Send S');
  sendS.style('width', '80px');
  sendS.position(320, commandsY);
  sendS.mousePressed(() => {
    serial.write('S');
  });
  sendT = createButton('Send T');
  sendT.style('width', '80px');
  sendT.position(420, commandsY);
  sendT.mousePressed(() => {
    serial.write('T');
  });
}

function draw() {
}

function connectPort() {
  if (!serial.opened()) {
    serial.open('MicroPython', 115200);
  } else {
    serial.close();
  }
}
```
