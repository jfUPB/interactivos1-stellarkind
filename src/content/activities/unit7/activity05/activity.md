#### Aplica lo aprendido

:::note[🎯 Enunciado]
Ahora que comprendes cómo funciona el sistema base, es tu turno de modificarlo. Vas a modificar una aplicación que 
controlarás desde el móvil.
:::

:::tip[Recursos]
*   El código completo y funcional del caso de estudio de la Unidad 7.
*   [Referencia de p5.js](https://p5js.org/reference/) (para ideas visuales y de interacción).
*   Tus notas y comprensión de las actividades SEEK.

:::

👣 **Pasos**:

1. Analiza la siguiente aplicación:

``` js
let particles = [];

function setup() {
  createCanvas(windowWidth, windowHeight);
  background(0, 20);
}

function draw() {
  if (mouseIsPressed) {
    particles.push(new Particle(mouseX, mouseY));
  }

  for (let i = particles.length - 1; i >= 0; i--) {
    particles[i].update();
    particles[i].display();
    if (particles[i].isDead()) {
      particles.splice(i, 1);
    }
  }

  if (particles.length === 0) background(0, 20);
}
class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D();
    this.lifespan = 255;
    this.size = random(5, 20);
    this.noiseOffset = random(1000);
  }

  update() {
    // Se utiliza ruido Perlin para definir la dirección de la fuerza
    let angle =
      noise(this.pos.x * 0.005, this.pos.y * 0.005, this.noiseOffset) *
      TWO_PI *
      2;
    let force = p5.Vector.fromAngle(angle);
    force.mult(0.5);
    // Se aplica la fuerza a la velocidad
    this.vel.add(force);
    this.vel.limit(4);
    this.pos.add(this.vel);
    // La vida de la partícula disminuye gradualmente
    this.lifespan -= 3;
  }

  display() {
    fill(150, 100, 255, this.lifespan);
    ellipse(this.pos.x, this.pos.y, this.size);
  }

  isDead() {
    return this.lifespan < 0;
  }
}

function keyPressed() {
  console.log(`particle size: ${particles.length}`);
}
```

2. Vas a modificar la aplicación anterior para que se convierta en la aplicación de escritorio.
    *   El móvil enviará la posición del toque (X/Y) al servidor.
    *   El escritorio recibirá la posición y dibujará una partícula en esa posición.
    *   La partícula se comportará como en el código original, pero ahora su posición inicial será la del toque del móvil.
    *   El móvil enviará el color y si las partículas se pintan o no con stroke.

3. Ten presente que la aplicación móvil enviará la posición del toque (X/Y), el color de la partícula y si se pinta o no con stroke. 
El escritorio recibirá estos datos y los usará para dibujar la partícula en la posición del toque.


:::note[🧐🧪✍️ Reporta en tu bitácora]
*   Explica los **cambios clave** que realizaste en `mobile/sketch.js`: ¿Qué datos envías ahora y cómo/cuándo los envías? Pega fragmentos de código relevantes.
*   Explica los **cambios clave** que realizaste en `desktop/sketch.js`: ¿Cómo recibes e interpretas los datos? ¿Qué modificaste en `setup()` o `draw()` para lograr el nuevo efecto? Pega fragmentos de código relevantes.
*   Si modificaste `server.js`, explica por qué fue necesario y qué cambiaste.
:::

:::caution[📤 Entrega]
*   Copia cada uno de los códigos: server, cliente móvil y cliente de escritorio.
*   Incluye en tu bitácora la explicación de los cambios (con código).
*   Añade un **ENLACE** a un video corto (PERO EDITADO) mostrando las pantallas del móvil y del 
escritorio. **NO OLVIDES: un enlace al video, no el video**
:::
