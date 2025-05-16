## Solución a la cuarta actividad

### Experimento 1
#### Refresca la página page2.html. Observa la consola del navegador. ¿Ves algún error relacionado con la conexión? ¿Qué indica?
Abrí page2.html en mi navegador con el servidor corriendo. Abrí la consola (F12). Luego detuve el servidor Node.js (Ctrl+C). Refresqué la página page2.html. Inmediatamente vi errores en la consola relacionados con la conexión de Socket.IO. Los mensajes indicaban que el cliente no podía conectar al servidor, estos mensajes indicaban claramente que mi página estaba intentando desesperadamente conectarse al servidor en ```localhost:3000```, pero no había nadie escuuchando.

Volví a iniciar el servidor (npm start) y refresqué la página. Los errores desaparecieron de la consola.

### Experimento 2
#### ¿Qué pasó en page1 antes de que movieras page2? ¿Tenía la información correcta sobre page2 desde el principio? ¿Por qué es útil enviar el estado inicial al conectarse? Descomenta la línea.
Comenté la línea ```socket.emit('win2update', currentPageData, socket.id);```. Reinicié el servidor y refresqué page1.html y page2.html. Movi page2 un poco para forzar que enviara una actualización después de la conexión inicial (porque la función ```checkWindowPosition``` enviaría actualizaciones al detectar el movimiento).
Antes de mover page2, la visualización en page1 no reflejaba la posición correcta de page2; mostraba la posición inicial por defecto de remotePageData (0,0 con tamaño 100x100; si es que no había utilizado el servidor antes en la sesión) o la última posición que page1 recordaba si ya se había conectado antes. La posición en page1 solo se actualizó a la correcta después de que yo moviera page2.

Enviar el estado inicial en el evento 'connect' asegura que, tan pronto como mi página se conecta al servidor, este recibe mi información actual (posición, tamaño) y puede compartírsela inmediatamente a las otras páginas. Así, las otras páginas pueden tener la información correcta sobre mí desde el principio, en lugar de mostrar datos viejos o por defecto hasta que yo haga algo que active un envío de datos. Asegura que la visualización esté sincronizada al momento de la conexión. 

### EXperimento 3
#### Mueve la ventana de page1. Observa la consola del navegador de page2. ¿Se dispara el log “Page 2 received…”? ¿Qué datos muestra?
Me aseguré de tener el console.log en mi page2.js. Abrí ambas páginas. Moví la ventana de page1. En la consola del navegador de page2, vi que el mensaje ```Page 2 received data about the other window: { x: ..., y: ..., width: ..., height: ... }``` aparecía constantemente mientras movía page1. Los datos mostrados eran la posición y el tamaño de page1. 

#### Mueve la ventana de page2. Observa la consola de page2. ¿Se dispara este log? ¿Por qué sí o por qué no? (Pista: ¿Quién envía el evento getdata?)
Moví la ventana de page2. Esta vez, el mensaje no apareció en la consola de page2.  Esto ocurrió porque el servidor, en su código, utiliza ```socket.broadcast.emit('getdata', ...)``` cuando recibe un mensaje de actualización. ```broadcast.emit``` envía el mensaje a todos los clientes conectados EXCEPTO al que envió el mensaje original. 
Por eso page1 recibió el mensaje getdata (y su visualización se actualizó), pero page2 no recibió su propio mensaje de vuelta.

### Experimento 4
#### Mueve la ventana de page2 muy lentamente. Observa la consola de page2. ¿Cuándo aparece el mensaje “Page 2 detected change…”?
Abrí ambas páginas. Moví la ventana de page2 muy lentamente. El mensaje "Page 2 detected change..." aparecía en la consola de page2. Si movía muy, muy lento, el mensaje no aparecía en cada frame, sino solo cuando la posición o el tamaño en píxeles cambiaba de un frame a otro.

#### Deja la ventana de page2 quieta. ¿Aparece el mensaje?
Dejé la ventana de page2 quieta. El mensaje dejó de aparecer.

#### ¿Por qué es eficiente esta estrategia de comparar con el estado anterior antes de enviar datos por la red? Anota tu reflexión.
Esta estrategia de comparar con el estado anterior antes de enviar datos por la red es muy eficiente porque evita enviar datos redundantes.
Si la ventana no se ha movido ni cambiado de tamaño, no hay necesidad de enviarle la misma información al servidor una y otra vez.

### Experimento 5
#### Cambiar el fondo con la distancia: 
Modifiqué background(220); para que dependiera de la distancia entre las esquinas superiores izquierdas de las ventanas. Cuando las ventanas estaban cerca, el fondo se ponía blanco o gris claro. A medida que las alejaba, el fondo se volvía más oscuro.

#### Tamaño del círculo local con el ancho remoto:
Modifiqué la llamada a drawCircle para el círculo local.
``` js
function draw() {
     background(220);
    fill(255, 0, 0);
    ellipse(point2[0], point2[1], remotePageData.width * 0.5, remotePageData.width * 0.5); // Radio basado en la mitad del ancho remoto
    // (resto del código de draw )
}
```
El tamaño del círculo en mi ventana cambiaba a medida que redimensionaba la otra ventana. Si hacía la otra ventana ancha, mi círculo se hacía grande; si la hacía estrecha, mi círculo se hacía pequeño.

#### Color de la línea con la posición relativa X: 
Modifiqué el stroke() para la línea:
``` js
function draw() {
     background(220);
     drawCircle(point2[0], point2[1]);
     checkWindowPosition();
     let vector1 = createVector(currentPageData.x, currentPageData.y);
     let vector2 = createVector(remotePageData.x, remotePageData.y);
     let resultingVector = createVector(vector2.x - vector1.x, vector2.y - vector1.y);

     // El color de la línea depende de si la otra ventana está a mi izquierda o derecha
     if (resultingVector.x < 0) { // Si la diferencia en X es negativa, la otra ventana está a mi izquierda
         stroke(0, 0, 255);
     } else { // Si la diferencia en X es positiva o cero, está a mi derecha o alineada verticalmente
         stroke(255, 165, 0);
     }
     strokeWeight(20);
     drawCircle(resultingVector.x + remotePageData.width / 2, resultingVector.y + remotePageData.height / 2);
     line(point2[0], point2[1], resultingVector.x + remotePageData.width / 2, resultingVector.y + remotePageData.height / 2);
 }
```
La línea se volvía azul si movía la otra ventana principalmente hacia la izquierda de mi ventana, y naranja si la movía hacia la derecha.
