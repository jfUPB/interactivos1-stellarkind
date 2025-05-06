## Solución a la primer actividad:



### Ejemplo #1) OpenProcessing:

- **Sketch original:** [Multi Circle Maze](https://openprocessing.org/sketch/2470557)  
    - **¿Por qué me llamó la atención?:** Me parece interesante este sketch porque combina patrones geométricos circulares con un uso muy atractivo del color. Me gusta cómo los laberintos formados por líneas curvas se entrelazan creando una composición dinámica y entretenida. La variedad de colores vivos y el contraste con el fondo oscuro me llaman mucho la atención, además de que la distribución aleatoria pero armoniosa de las formas genera un efecto visual hipnótico.
 
- **Mi versión del sketch:** [actividad04-01-openprocessing](https://editor.p5js.org/stellarkind/full/-m3a4ILCM)
    - **Descripción de los cambios:**
      
        1. **Adición de Nuevas Paletas de Colores:**
            - **Cambio en el código:** Añadí varios nuevos arrays de colores a la constante palettes al principio del archivo ```sketch.js```. Cada uno de estos arrays representa una nueva combinación de colores (fondo y colores para las líneas del patrón).
            - **Por qué:** La constante palettes es de donde el sketch selecciona aleatoriamente la combinación de colores cada vez que se llama a la función ```generate()```. Al añadir más opciones a este array (mis paletas de solarpunk, cyberpunk, grises, morados, etc.), aumento la variedad visual del sketch. Ahora, cada vez que se genera un nuevo patrón (ya sea al inicio o al hacer clic), hay una mayor probabilidad de obtener una combinación de colores diferente, haciendo que la experiencia sea más dinámica y se ajuste a mis preferencias estéticas.
              
        2. **Inicio del Patrón en la Posición del Clic del Ratón:**
            - **Cambio en el código:**
              - La función ```mousePressed()``` fue modificada para llamar a ```generate()``` pasando las coordenadas actuales del ratón (mouseX, mouseY) como argumentos: generate(```mouseX```, ```mouseY```);.
              - La función ```generate()``` fue modificada para aceptar opcionalmente dos parámetros (```x```, ```y```). Dentro de ```generate()```, se añadió una verificación: si se reciben ```x``` e ```y``` (lo que ocurre cuando se llama desde mousePressed), se usa ```createVector(x, y)``` para establecer la variable cnter (el centro inicial del patrón). Si no se reciben argumentos (lo que ocurre cuando ```generate()``` es llamada desde ```setup()``` al cargar la página), ```cnter``` se sigue determinando tomando un punto aleatorio del muestreo de ```Poisson```, como en el código original. También se añadieron llamadas a ```addRing()``` después de establecer el centro por clic, para asegurar que el crecimiento comience inmediatamente.
            - **Por qué:** Originalmente, la función ```generate()``` siempre seleccionaba un punto inicial ```cnter``` de forma aleatoria a partir de los puntos generados por el muestreo de Poisson en todo el lienzo. Al modificar ```mousePressed()``` para pasar la posición del ratón y ajustar ```generate()``` para usar esas coordenadas, le das al usuario control directo sobre dónde comienza el patrón. Ahora, cada clic en la pantalla no solo genera un nuevo patrón, sino que también determina su punto de origen, permitiendo interacciones más intencionadas con el sketch.



### Ejemplo #2) Generative Design:

- **Sketch original:** [P_2_1_1_02](http://www.generative-gestaltung.de/2/sketches/?01_P/P_2_1_1_02)  
    - **¿Por qué me llamó la atención?:** Este sketch me parece interesante porque combina dos cosas que me atraen mucho: lo visualmente llamativo de los patrones generativos tipo laberinto y la interacción directa con el mouse. Me gusta que cada clic genera una composición diferente, lo que hace que nunca se repita y siempre me sorprenda con nuevas formas. Además, el hecho de que el grosor de las líneas reaccione al movimiento del mouse en los ejes X e Y me hace sentir más conectada con lo que está ocurriendo en pantalla, como si yo misma estuviera influyendo en la intensidad y la complejidad del diseño. Esa combinación de aleatoriedad controlada e interacción me parece muy chévere.

- **Mi versión del sketch:** [actividad04-01-generativedesign](https://editor.p5js.org/stellarkind/full/Ls7qAmZr_)
    - **Descripción de los cambios:**
        
        1. **Definición de Paletas de Colores Usando Cadenas Hexadecimales:**
            - **Cambio en el código:** Modifiqué la definición del array palettes para que cada color se representara como una cadena de texto en formato hexadecimal en lugar de usar directamente la función ```color()```.
            - **Por qué:** Lo hice para resolver un error (ReferenceError: color is not defined) que ocurrió al intentar usar la función ```color()``` fuera de las funciones ```setup()``` o ```draw()```. Al usar cadenas hexadecimales, el array palettes se puede definir globalmente sin depender de que p5.js esté completamente inicializado. La conversión a objetos p5.Color se realiza más adelante, cuando es seguro.
        
        2. **Función ```selectRandomPalette()```:**  
            - **Cambio en el código:** Ahora, después de seleccionar un array de paleta, la función utiliza el método ```.map(c => color(c))``` para convertir todas las cadenas de esa paleta a objetos ```p5.Color``` y almacenarlos en la variable ```currentPalette```. Luego, selecciona ```colorLeft``` y ```colorRight``` de entre estos objetos ```p5.Color``` (excluyendo el primer color, que es el fondo).
            - **Por qué:** Este es el paso donde las cadenas hexadecimales se convierten a objetos ```p5.Color``` que p5.js puede usar para dibujar. Al hacerlo dentro de una función que se llama después de ```setup()``` (desde ```setup()``` y ```mousePressed```), nos aseguramos de que la función ```color()``` esté disponible y evitamos errores. Además, la lógica mejorada para seleccionar ```colorLeft``` y ```colorRight``` garantiza que se elijan colores válidos de la paleta seleccionada, incluso en casos con pocos colores.

        3. **Integración de ```selectRandomPalette()``` en ```setup()``` y ```mousePressed()```:**
            - **Cambio en el código:** Se añadió una llamada a ```selectRandomPalette()``` al final de la función ```setup()``` y dentro de la función ```mousePressed()```.
            - **Por qué:** Llamar a ```selectRandomPalette()``` en ```setup()``` asegura que se seleccione una paleta aleatoria y se establezcan los colores iniciales tan pronto como el sketch comience. Llamarla en ```mousePressed()``` hace que la paleta cambie aleatoriamente cada vez que el usuario haga clic en la pantalla, añadiendo variedad visual a los diferentes patrones que se generan con cada clic.
          
        4. **Actualización de la Lógica de Teclado:**
            - **Cambio en el código:**  La función ```keyReleased()``` fue modificada. La tecla '4' ahora simplemente llama a ```selectRandomPalette()``` para seleccionar una nueva combinación de colores para las líneas de la paleta actual. Se eliminaron la lógica específica para las teclas '5', '6' y '7' que alternaban colores fijos y transparencia, ya que la gestión de colores ahora se centraliza en la selección de paletas. La tecla '0' ahora resetea el ```strokeCap``` y también llama a ```selectRandomPalette()```.
            - **Por qué:** Estos cambios simplifican la interfaz de teclado y la alinean con la nueva forma de manejar los colores a través de paletas. La tecla '4' ahora permite alternar entre las diferentes combinaciones de colores disponibles en la paleta seleccionada actualmente, mientras que el clic o la tecla '0' seleccionan una paleta completamente nueva.
      
        5. **Implementación de la Ondulación Interactiva al Mantener Presionado el Ratón:**
            - **Cambio en el código:** Se añadió un bloque condicional ```if (mouseIsPressed)``` dentro del bucle anidado en la función ```draw()```.
            - **Por qué:** Al dibujar la línea como una serie de puntos y desplazar cada punto basado en una onda que varía con el tiempo y la cercanía del ratón, se crea el efecto de ondulación. Vincular la semilla aleatoria a frameCount cuando se presiona el ratón también contribuye a la apariencia dinámica de la ondulación, mejorando la interacividad del sketch. 

### Ejemplo #3) p5js.org/examples:

- **Sketch original:** [3D Orbit Control](https://p5js.org/examples//3d-orbit-control/)  
    - **¿Por qué me llamó la atención?:** Lo que me parece tan chévere de este ejemplo es la capacidad de interactuar con una escena tridimensional de forma tan fluida y sencilla utilizando solo el ratón. Poder rotar, desplazar y hacer zoom en el espacio 3D con orbitControl() abre un sinfín de posibilidades para crear visualizaciones, modelos interactivos o juegos simples. Muestra el potencial de p5.js más allá del 2D y cómo manejar la cámara en un entorno 3D, con un código relativamente sencillo.

- **Mi versión del sketch:** [actividad04-01-p5js.org/example](https://editor.p5js.org/stellarkind/full/HeDAQz27v)
    - **Descripción de los cambios:**

      1. **Definir mi Paleta de Colores Favorita:**
            - **Cambio en el código:** En el código original, los colores eran fijos o dependían de ```normalMaterial()```. Yo quería usar una paleta específica. Añadí una constante llamada ```myFavoritePaletteStrings``` al inicio del sketch, donde guardé los colores de mi paleta favorita como cadenas de texto hexadecimal: ["#1a1a2e", "#a74ac7", "#f3c623", "#24F11C"]. Además, en la función ```setup()```, convertí estas cadenas a objetos de color de p5.js usando ```.map(c => color(c))``` y los guardé en la variable ```currentPalette```.
            - **Por qué:** Hacer esto me permitió tener mis colores deseados definidos en un solo lugar y listos para ser usados.

        2. **Aplicar Colores de la Paleta al Fondo y las Formas:**
            - **Cambio en el código:** En la función ```draw()```, cambié la línea que dibuja el fondo ```(background())``` para que usara el primer color de mi ```currentPalette```. Este color ("#1a1a2e", un tono oscuro) se convirtió así en el color de fondo de mi escena 3D. Para los objetos (que serían las estrellas), necesitaba ciclar a través de los otros colores de mi paleta ("#a74ac7", "#f3c623", "#24F11C"). Añadí una variable ```shapeIndex``` en ```draw()``` que se incrementa con cada forma dibujada en los bucles. Luego, calculé un índice de color usando el operador módulo (%) con el número de colores disponibles en la paleta (excluyendo el fondo) y usé este índice para seleccionar un color de currentPalette para cada estrella.
            - **Por qué:** Quería que mi paleta de colores se aplicara a toda la escena, dando un aspecto cohesivo y personalizado a las estrellas y el fondo.

        3. **Reemplazar Cubos por Estrellas:**
            - **Cambio en el código:** El cambio visual más grande fue reemplazar los ```box()``` del ejemplo original por estrellas. Dado que p5.js no tiene una primitiva 3D para estrellas, necesité una forma de dibujarlas. Aquí fue donde Gemini me ayudó: se añadió una nueva función llamada ```draw3DStar()```. Esta función toma el tamaño de los radios exterior e interior de la estrella, el número de puntas y un color, y dibuja una estrella plana en 3D usando ```beginShape()```, ```vertex()``` y ```endShape(CLOSE```).  También activé ```ambientLight()``` y ```directionalLight()``` en ```setup()```, ya que ```draw3DStar``` usa ```fill()``` en lugar de ```normalMaterial()```, y la iluminación es necesaria para que los colores y la forma de la estrella se vean correctamente en 3D (Gemini me ayudó con eso porque no se visualizaban bien las formas).
            - **Por qué:** Quería un elemento visual diferente a las cajas para hacer la escena más interesante y única, y las estrellas encajaban bien con la idea de un espacio 3D.
