## Solución a la cuarta actividad

### Compara el código original del caso de estudio con el anterior. ¿Qué notas de diferente?
Se añadió una nueva línea ```<script src="https://unpkg.com/@gohai/p5.webserial@^1/libraries/p5.webserial.js"></script>```. Esto es muy importante, porque esta línea carga la librería p5.webserial, que es la que me va a permitir que mi sketch en p5.js se comunique con dispositivos seriales como el micro:bit a través del puerto USB, la que ya hemos sado en actividades anteriores.  

###  Reflexiona ¿Para qué se usan estas imágenes? ¿Qué representan?
Ejecuté el sketch. No hubo errores aparentes. Las imágenes se cargan en el array ```lineModule``` de la función ```preload()```, por lo qu creo que estarán listas para ser utilizadas por el sketch antes de que empiece a dibujar. Revisando el sketch original de nuevo, recordé que estas imágenes son las "formas" que dibuja el pincel generativo cuando no se selecciona la línea simple.

### ¿Puedes notar que no tenemos a INIT en este caso? Pero la verdad si está, solo que no como antes, la función setup() está haciendo esa función. ¿Lo ves?
Ajá, en unidades anteriores teníamos un pseudoestado INIT, pero aquí no lo tenemos explícitamente. En este caso la función ```setup()``` actúa como ese estado INIT, haciendo el trabajo de inicialización.

### 

