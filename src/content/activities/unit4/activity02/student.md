## Solución a la segunda actividad:
### Explicando el código del sketch
El archivo ```index.html``` es el archivo que configura la página web. 
Carga la librería ```p5.js``` (que proporciona todas las funciones de dibujo y manejo de eventos) y el archivo ```sketch.js``` que contiene la lógica del programa. También incluye enlaces a librerías adicionales. 
Dentro del archivo ```sketch.js```, el código está organizado de la siguiente manera:
- Al principio, se declaran varias variables que guardan información sobre el estado del pincel y el dibujo:
  - ```lineModuleSize```: Controla el tamaño del elemento que se dibuja en cada paso.
  - ```angle```: Almacena el ángulo de rotación actual del elemento del pincel.
  - ```angleSpeed```: Determina cuánto cambia el ángulo de rotación en cada fotograma, controlando así la velocidad de rotación.
  - ```lineModule```: Es un array que contendrá las diferentes formas de "pincel" cargadas (imágenes SVG) o representará el pincel de línea simple.
  - ```lineModuleIndex```: Indica cuál de las formas en lineModule se está utilizando actualmente (0 para la línea, 1-4 para los SVGs cargados).
  - ```c```: Guarda el color actual con el que se está dibujando.
  - ```clickPosX```, ```clickPosY```: Almacenan la posición X e Y donde se hizo clic con el ratón por primera vez al iniciar un arrastre; se usan para la restricción de movimiento con SHIFT.
- La función ```preload()``` se ejecuta antes de que comience la parte principal del sketch. Carga los archivos de imagen SVG (02.svg a 05.svg) desde una carpeta ```data``` y los guarda en el array ```lineModule``` (en los índices 1 a 4). Esto asegura que las imágenes de las formas del pincel estén listas para ser utilizadas cuando se necesiten.
- La función ```setup()``` que se ejecuta una vez al inicio hace lo siguiente: Crea el lienzo de dibujo ocupando el ancho y alto de la ventana del navegador, le da un color blanco al fondo, oculta el raton cuando está sobre el lienzo y le da un grosor a la línea.
- La función ```windowResized()``` se encarga de ajustar el tamaño del lienzo para que siempre llene la ventana del navegador por si cambia de tamaño.
- La función ```draw()``` es el bucle principal que se ejecuta repetidamente para dibujar en la pantalla, que a través de las coordenads del ratón y verificando si alguna de las teclas que cambia las características del pincel está presionada permite al usuario dibujar y que el lienzo guarde la configuración de dibujo.
- La función ```mousePressed()``` se ejecuta una vez cada vez que se presiona un botón del ratón para establecer el tamaño inicial aleatorio para la forma del pincel, así como guardar la posición del mismo.
- La función ```keyPressed()``` se activa continuamente mientras una tecla se mantiene presionada. Se utiliza para modificar el tamaño del pincel (```lineModuleSize```) con las flechas arriba/abajo y la velocidad de rotación (```angleSpeed```) con las flechas izquierda/derecha.
- La función ```keyReleased()``` se ejecuta una vez cuando se suelta una tecla, asi administra lo que ocurre al presionar cualquiera de las teclas que modifican el lienzo y características del pincel.
- Los archivos SVG en la carpeta ```data``` son las imágenes que se cargan en ```lineModule``` y se utilizn como las formas visuales del pincel cuando se seleccionan los índices 1 a 4. Contienen definiciones vectoriales de diferentes patrones o diseños.
### Experimentando con el sketch
![250506_84009_771](https://github.com/user-attachments/assets/2e34cbd3-5396-49cd-822e-eb1d3fe47df3)
Este sketch funciona como una herramienta de dibujo donde el pincel tiene propiedades dinámicas, que al empezar a experimentar si conocer el código parecen algo impredecibles. La apariencia del trazo que se genera depende de cómo interactuamos con el mouse y el teclado a la vez. Estos son algunas de las técnicas para dibujar en el sketch que pude identificar:
- **Hacer trazos en espiral o circulares:** Moviendo el mouse en círculos mientras la velocidad de rotación (angleSpeed) no es cero. Una velocidad alta crea espirales abiertas o círculos discontinuos; una velocidad baja crea espirales cerrados.
- **Patrones repetitivos:** Arrastrando el ratón lentamente con un tamaño de pincel y velocidad de rotación específicos. Cada "sello" se dibuja cerca del anterior con una ligera rotación.
- **Líneas rectas texturizadas:** Manteniendo presionada la tecla SHIFT mientras se arrastra el mouse. Esto fuerza el movimiento a ser horizontal o vertical, mientras que el elemento del pincel sigue rotando en esa línea recta, creando una textura a lo largo del trazo.
- **Dibujos caóticos o abstractos (mi favorita):** Cambiando rápidamente las propiedades del pincel (color con ESPACIO, tamaño con flechas, velocidad de rotación con flechas, forma con teclas '5'-'9') mientras se arrastra el mouse.
