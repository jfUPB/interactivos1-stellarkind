## Solución a la novena actividad  
### Programa en p5.js que genere patrones visuales aleatorios utilizando funciones matemáticas simples:  
``` js
let baseColor;

function setup() {
  createCanvas(600, 600);
  noLoop();
  baseColor = [random(100, 255), random(100, 255), random(100, 255)]; // Color principal, del que va a sacar los otros 
}

function draw() {
  background("#E0BAD7"); //pone el fondo rosadito
  let cols = 10; //numero de columnas
  let rows = 10; //numero de filas
  let scale = width / cols;  

  for (let y = 0; y < rows; y++) {
    for (let x = 0; x < cols; x++) {
      let centerX = x * scale + scale / 2;
      let centerY = y * scale + scale / 2;
      let radius = random(10, scale / 2);  // genera una cuadrícula de círculos de diferentes tamaños y colores

      // Variaciondel color
      let r = constrain(baseColor[0] + random(-30, 30), 0, 255);
      let g = constrain(baseColor[1] + random(-30, 30), 0, 255);
      let b = constrain(baseColor[2] + random(-30, 30), 0, 255);
      
      fill(r, g, b);
      ellipse(centerX, centerY, radius * 2); //dibuja los circulos
    }
  }
}
```
