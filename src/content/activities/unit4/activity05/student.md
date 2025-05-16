## Solución a la quinta actividad
### Enlace a mi aplicación original:
[Multi Circle Maze Por Stella V.1](https://editor.p5js.org/stellarkind/full/-m3a4ILCM)
### Código de p5.js para la versión modificada
```js
let port;
let connectBtn;
let microBitConnected = false;

// Variables del patrón generativo
let freePos, cnter, last, d, rings, trails;
let searchDS, candPoints, finished;
let palette, bkg, icolor;
const batchSize = 10;

// Estados de la aplicación
const STATES = {
  WAIT_MICROBIT_CONNECTION: "WAIT_MICROBIT_CONNECTION",
  RUNNING: "RUNNING",
};
let appState = STATES.WAIT_MICROBIT_CONNECTION;

// Datos del micro:bit
let microBitX = 0;
let microBitY = 0;
let microBitAState = false;
let microBitBState = false;
let prevMicroBitAState = false;
let prevMicroBitBState = false;

// Paletas de colores
const palettes = [
  ["#f0e6d2", "#2B7C3F", "#f3c623", "#c8d8b9", "#a4b494", "#1D9E8F"],
  ["#0f1c16", "#2B7C3F", "#f3c623", "#c8d8b9", "#a4b494", "#1D9E8F"],
  ["#000000", "#8a2be2", "#ff00ff", "#444444", "#cccccc"],
  ["#ffffff", "#000000", "#333333", "#666666", "#999999", "#cccccc"],
  ["#000000", "#ffffff", "#333333", "#666666", "#999999", "#cccccc"],
  ["#2d004d", "#4a0080", "#6a00b8", "#8d00ff", "#b266ff"],
  ["#8d00ff", "#2d004d", "#4a0080", "#6a00b8", "#b266ff"],
  ["#1a1a2e", "#a74ac7", "#f3c623", "#24F11C"],
  ["#f0f4f8", "#a74ac7", "#f3c623", "#24F11C"]
];

function setup() {
  createCanvas(windowWidth, windowHeight);
  
  // Configuración de la conexión serial
  port = createSerial();
  
  // Crear botón de conexión con estilo
  connectBtn = createButton("Conectar micro:bit");
  connectBtn.position(20, 20);
  connectBtn.style('padding', '10px 15px');
  connectBtn.style('background-color', '#4CAF50');
  connectBtn.style('color', 'white');
  connectBtn.style('border', 'none');
  connectBtn.style('border-radius', '4px');
  connectBtn.style('font-size', '16px');
  connectBtn.mousePressed(connectBtnClick);
  
  // Inicialización del patrón generativo
  initializePattern(width/2, height/2);
}

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200).then(() => {
      microBitConnected = true;
      connectBtn.html("Desconectar micro:bit");
      connectBtn.style('background-color', '#f44336');
      console.log("Micro:bit conectado");
    }).catch(err => {
      console.error("Error al conectar:", err);
    });
  } else {
    port.close();
    microBitConnected = false;
    connectBtn.html("Conectar micro:bit");
    connectBtn.style('background-color', '#4CAF50');
    console.log("Micro:bit desconectado");
  }
}

function initializePattern(x = width/2, y = height/2) {
  // Seleccionar paleta aleatoria inicial
  palette = random(palettes);
  bkg = palette[0];
  palette = palette.slice(1);
  
  // Tamaño base de los elementos
  d = min(width, height) / 40;
  
  // Configurar puntos iniciales
  candPoints = new Poisson(0, 0, width, height, d);
  cnter = createVector(x, y);
  
  // Inicializar estructuras de datos
  freePos = [];
  trails = [[cnter]];
  last = {
    p: cnter,
    center: cnter,
    ring: 0,
    ang: 0,
    free: false,
  };
  rings = 0;
  searchDS = new SearchDS(-d, -d, width + d * 2, height + d * 2);
  addToDS(last);
  finished = false;
  
  // Añadir anillos iniciales
  addRing();
  addRing();
  addRing();
}

function updateButtonStates(newAState, newBState) {
  // Botón A presionado: activa el modo dibujo (como clic sostenido)
  if (newAState === true && prevMicroBitAState === false) {
    console.log("Botón A presionado - Iniciando dibujo");
  }
  
  // Botón A liberado: detiene el modo dibujo
  if (newAState === false && prevMicroBitAState === true) {
    console.log("Botón A liberado");
  }
  
  // Botón B presionado: cambiar paleta de colores
  if (newBState === true && prevMicroBitBState === false) {
    palette = random(palettes);
    bkg = palette[0];
    palette = palette.slice(1);
    console.log("Botón B presionado - Cambiando paleta de colores");
  }
  
  prevMicroBitAState = newAState;
  prevMicroBitBState = newBState;
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

function draw() {
  // Manejo de la conexión serial
  if (!port.opened()) {
    microBitConnected = false;
  } else {
    microBitConnected = true;
    
    // Leer datos del micro:bit
    if (port.availableBytes() > 0) {
      let data = port.readUntil("\n");
      if (data) {
        data = data.trim();
        let values = data.split(",");
        if (values.length === 4) {
          // Mapear valores del acelerómetro a la pantalla
          microBitX = map(int(values[0]), -1024, 1024, 0, width);
          microBitY = map(int(values[1]), -1024, 1024, 0, height);
          microBitAState = values[2].toLowerCase() === "true";
          microBitBState = values[3].toLowerCase() === "true";
          updateButtonStates(microBitAState, microBitBState);
        }
      }
    }
  }
  
  // Máquina de estados de la aplicación
  switch (appState) {
    case STATES.WAIT_MICROBIT_CONNECTION:
      background(50);
      textAlign(CENTER, CENTER);
      fill(255);
      textSize(24);
      text("Esperando conexión con micro:bit...", width/2, height/2);
      textSize(16);
      text("Presiona el botón 'Conectar micro:bit'", width/2, height/2 + 40);
      
      if (microBitConnected) {
        appState = STATES.RUNNING;
      }
      break;
      
    case STATES.RUNNING:
      if (!microBitConnected) {
        appState = STATES.WAIT_MICROBIT_CONNECTION;
        break;
      }
      
      // Actualizar el centro del patrón con los datos del acelerómetro
      cnter.x = lerp(cnter.x, microBitX, 0.1);
      cnter.y = lerp(cnter.y, microBitY, 0.1);
      
      // Dibujar el patrón generativo
      drawPattern();
      
      // Si el botón A está presionado, continuar generando el patrón
      if (microBitAState) {
        generatePatternStep();
      }
      
      // Mostrar información de estado
      drawStatusInfo();
      break;
  }
}

function drawPattern() {
  background(bkg);
  noFill();
  strokeJoin(ROUND);
  strokeWeight(d / 2);
  
  // Dibujar trails
  let icolor = 0;
  for (let trail of trails) {
    stroke(palette[icolor]);
    icolor = (icolor + 1) % palette.length;
    beginShape();
    for (let p of trail) {
      vertex(p.x, p.y);
    }
    endShape();
  }
}

function drawStatusInfo() {
  // Mostrar información de conexión y controles
  fill(0, 100);
  noStroke();
  rect(10, 60, 300, 100, 5);
  
  fill(255);
  textSize(14);
  textAlign(LEFT);
  text(`Estado: ${microBitConnected ? "Conectado" : "Desconectado"}`, 20, 80);
  text(`Posición: ${floor(cnter.x)}, ${floor(cnter.y)}`, 20, 100);
  text(`Botón A: ${microBitAState ? "Presionado" : "No presionado"}`, 20, 120);
  text(`Botón B: ${microBitBState ? "Presionado" : "No presionado"}`, 20, 140);
}

function generatePatternStep() {
  if (finished) return;
  
  let placed = false;
  let tries = 0;
  let bestDist = Infinity, best = -1;
  let lastPos = last.p;
  
  while (!placed && tries++ < 2) {
    if (freePos.length == 0) {
      if (random() < 0.4) {
        do {
          if (candPoints.length == 0) {
            finished = true;
            return;
          }
          cnter = candPoints.pop();
        } while (!neighborsWithin(cnter, d - 0.1).next().done);
        rings = 0;
      }
      addRing();
      addRing();
    }
    
    for (let neigh of freeNeighbors(lastPos, d * 2)) {
      let dlast = neigh.p.dist(lastPos);
      if (dlast < bestDist) {
        best = neigh;
        bestDist = dlast;
        placed = true;
      }
    }
  }
  
  if (!placed) {
    if (freePos.length == 0) return;
    best = freePos.pop();
    bestDist = best.p.dist(lastPos);
  } else {
    let k = freePos.length - 1;
    freePos[best.index] = freePos[k];
    freePos[best.index].index = best.index;
    freePos.pop();
  }
  
  let prev = last;
  last = best;
  last.free = false;
  
  if (bestDist < d * 1.42) {
    if (prev.ring != last.ring || prev.center != last.center) {
      trails[trails.length - 1].push(last.p);
    } else {
      let ang0 = (prev.ang + TAU) % TAU;
      let ang1 = (last.ang + TAU) % TAU;
      let dang = ang1 - ang0;
      
      if (dang < -PI) dang = ang1 + TAU - ang0;
      else if (dang > PI) dang = ang1 - ang0 - TAU;
      
      let n = int(abs(dang) / radians(1));
      dang /= n;
      let c = prev.center;
      let v = createVector(d * prev.ring, 0).rotate(ang0);
      
      for (let i = 0; i < n; i++) {
        v.rotate(dang);
        trails[trails.length - 1].push(c.copy().add(v));
      }
    }
  } else {
    trails.push([last.p]);
  }
}

// Funciones auxiliares del patrón generativo (se mantienen igual)
function addToDS(obj) {
  let mbr = MBR.fromRect(obj.p.x - d, obj.p.y - d, d * 2, d * 2);
  if (obj.free) {
    obj.index = freePos.length;
    freePos.push(obj);
  }
  searchDS.add(mbr, obj);
}

function* freeNeighbors(p, dmax = d) {
  let mbr = MBR.fromRect(p.x - dmax, p.y - dmax, dmax * 2, dmax * 2);
  if (mbr.containedIn(searchDS.mbr))
    for (let obj of searchDS.valuesInMbr(mbr)) {
      if (obj.free) yield obj;
    }
}

function* neighborsWithin(p, dmax) {
  let mbr = MBR.fromRect(p.x - dmax, p.y - dmax, dmax * 2, dmax * 2);
  for (let obj of searchDS.valuesInMbr(mbr)) {
    if (obj.p.dist(p) <= dmax) yield obj;
  }
}

function addRing() {
  rings++;
  let r = d * rings;
  let perimeter = TAU * r;
  let n = floor(perimeter / d);
  let dAng = TAU / n;
  let ang = random(TAU);
  let v = createVector(r, 0);

  for (let i = 0; i < n; i++) {
    let p = cnter.copy().add(v.copy().rotate(ang));
    if (searchDS.mbr.contains(p, d / 2) && neighborsWithin(p, d - 0.1).next().done) {
      addToDS({
        p,
        center: cnter,
        ring: rings,
        ang,
        free: true,
      });
    }
    ang += dAng;
  }
}

function keyPressed() {
  if (key === 's' || key === 'S') {
    saveCanvas('generative_pattern', 'png');
  }
  if (key === ' ') {
    initializePattern(random(width), random(height));
  }
}
```
Cabe aclarar que hay otros archivos .js que utiliza el sketch, de los cuales provenientes de mi version original del sketch quedaron igualitos, lo unico que cambié fue el index.hmtl para agregar la librería para establecer la conexión serial. Y de hecho esta versión modificada quedó aún más alejada del programa original de [Multi Circle Maze por CLAUDIO ESPERANÇA](https://openprocessing.org/sketch/2470557), pero de hecho me gusta más porque permite mayor control por parte del usuario sobre lo que sea que dibuje en el canvas gracias al acelerómetro y a que introduce la opción de dibujar manteniendo presionado.

### La aplicación modificada para trabajar con el micro:bit
[Multi Circle Maze Por Stella V.2.0](https://editor.p5js.org/stellarkind/full/u6qdUxxPt)

### Canvas con la aplicación en su versión 2.0:
![image](https://github.com/user-attachments/assets/6f46feac-98db-48ba-a335-e5bbe8e5bad7)
