#### Explorando los clientes (p5.js + Socket.IO)

:::note[🎯 **Enunciado**] 
Ahora nos enfocaremos en cómo uno de los clientes, page2.js, 
interactúa con el servidor y visualiza la información. El código de page1.js es 
muy similar, así que entender uno te ayudará a entender el otro.
:::

##### 1. Variables globales y conexión inicial

``` js
// page2.js 
let currentPageData = { /* ... obtiene datos de la ventana ... */ };
let remotePageData = { x: 0, y: 0, width: 100, height: 100 }; 
let point2 = [currentPageData.width / 2, currentPageData.height / 2]; 

// Conexión al servidor Socket.IO que corre en localhost:3000
let socket = io('http://localhost:3000'); 
```

:::note[🧩 **Explicación**]

currentPageData: un objeto que almacena la posición (screenX, screenY) y tamaño (innerWidth, innerHeight) de esta ventana del navegador.

remotePageData: un objeto para almacenar los datos que recibiremos sobre la otra ventana (page1). Se inicializa con valores por defecto.

point2: coordenadas del centro de esta ventana, para dibujar.

socket = io('http://localhost:3000');: ¡Aquí el cliente inicia la conexión! Llama a la función io() (proporcionada por la librería Socket.IO que cargamos en el HTML) pasándole la dirección del servidor. Esto intenta establecer la conexión WebSocket.
:::


:::caution[🧐🧪✍️ **Experimenta**]

- Abre page2.html en tu navegador (con el servidor corriendo).

- Abre la consola de desarrollador (F12).

- Detén el servidor Node.js (Ctrl+C).

- Refresca la página page2.html. Observa la consola del navegador. ¿Ves algún error relacionado con la conexión? ¿Qué indica?

- Vuelve a iniciar el servidor y refresca la página. ¿Desaparecen los errores?
:::

##### 2. Manejando la conexión establecida

``` js
// Evento: se dispara cuando la conexión con el servidor se establece
socket.on('connect', () => {
    console.log('Connected to server - My ID:', socket.id);
    // Envía el estado inicial de esta ventana al servidor
    socket.emit('win2update', currentPageData, socket.id); 
});
```

:::note[🧩 **Explicación**] 

* socket.on('connect', ...): este oyente se dispara cuando la conexión iniciada con io() se completa exitosamente.

* console.log(...): muestra un mensaje y el ID único asignado por el servidor a esta conexión de cliente.

* socket.emit('win2update', currentPageData, socket.id);: ¡El cliente envía su primer mensaje!

    * emit: función para enviar un mensaje al servidor.

    * 'win2update': es el nombre del evento que estamos enviando. Debe coincidir con el que el servidor está escuchando (socket.on('win2update', ...) en server.js).

    * currentPageData: son los datos que enviamos (la posición y tamaño de esta ventana).

    * socket.id: enviamos también nuestro ID (aunque en este caso particular el servidor ya lo sabe, es una práctica común).

* Importante: esto asegura que, tan pronto como nos conectamos, el servidor recibe nuestro estado inicial.
:::


:::caution[🧐🧪✍️ **Experimenta**]

- Comenta la línea socket.emit('win2update', currentPageData, socket.id); dentro del oyente connect.

- Reinicia el servidor y refresca page1.html y page2.html.

- Mueve la ventana de page2 un poco para que envíe una actualización.

- ¿Qué pasó en page1 antes de que movieras page2? ¿Tenía la información correcta sobre page2 
desde el principio? ¿Por qué es útil enviar el estado inicial al conectarse? Descomenta la línea.
:::

##### 3. Recibiendo datos del servidor

``` js
// Evento: se dispara cuando se recibe un mensaje 'getdata' del servidor
socket.on('getdata', (dataWindow) => {
    // Actualiza los datos de la ventana remota con la información recibida
    remotePageData = dataWindow; 
    console.log('Page 2 received data about the other window:', remotePageData); 
});
```

:::caution[🧩 **Explicación**] 

socket.on('getdata', ...): este ``event listener`` espera mensajes con el nombre 'getdata' que provienen 
del servidor. Recuerda que el servidor hace broadcast.emit('getdata', ...) cuando la otra 
ventana (page1) envía una actualización.

dataWindow: es la variable que contiene los datos enviados por el servidor (la información de page1).

remotePageData = dataWindow;: actualizamos nuestra variable local remotePageData con la información 
fresca de la otra ventana.

console.log(...): verificamos en la consola qué datos estamos recibiendo.
:::


:::caution[🧐🧪✍️ **Experimenta**]

- Asegúrate de tener este console.log en page2.js.

- Abre ambas páginas.

- Mueve la ventana de page1. Observa la consola del navegador de page2. ¿Se dispara el log "Page 2 received..."? ¿Qué datos muestra?

- Mueve la ventana de page2. Observa la consola de page2. ¿Se dispara este log? ¿Por qué sí o por qué no? (Pista: ¿Quién envía el evento getdata?)
:::

##### 4. Detectando cambios y enviando actualizaciones

``` js
let previousPageData = { /* ... inicializado igual que currentPageData ... */ };

function checkWindowPosition() {
    currentPageData = { /* ... obtiene datos actuales de la ventana ... */ };

    // Compara el estado actual con el anterior
    if (currentPageData.x !== previousPageData.x || currentPageData.y !== previousPageData.y || 
        currentPageData.width !== previousPageData.width || currentPageData.height !== previousPageData.height) {
        
        console.log("Page 2 detected change, sending update:", currentPageData); // Log para saber cuándo enviamos
        point2 = [currentPageData.width / 2, currentPageData.height / 2]; 
        // Si hubo cambios, envía el nuevo estado al servidor
        socket.emit('win2update', currentPageData, socket.id); 
        previousPageData = currentPageData; // Actualiza el estado anterior para la próxima comparación
    }
}

function draw() {
    // ... (código de dibujo) ...
    checkWindowPosition(); // Llama a la función en cada frame
    // ... (más código de dibujo usando remotePageData) ...
}
```

:::caution[🧩 **Explicación**]

* previousPageData: una variable para recordar cómo estaba la ventana en el frame anterior.

* checkWindowPosition(): esta función se llama repetidamente desde draw().

    * Obtiene el estado actual de la ventana en currentPageData.

    * El if compara cada propiedad actual con la guardada en previousPageData. Si alguna es diferente, significa que la ventana se movió o redimensionó.

    * Solo si hubo cambio:

        * Imprime un log.

        * Recalcula el centro point2.

        * Envía la actualización al servidor con socket.emit('win2update', ...).

        * ¡Crucial! Actualiza previousPageData = currentPageData para que la próxima vez comparemos contra el estado actual.

* Llamar a checkWindowPosition() dentro de draw() asegura que estamos verificando cambios constantemente.
:::


:::caution[🧐🧪✍️ **Experimenta**]

- Añade el console.log("Page 2 detected change...") dentro del if si no estaba.

- Abre ambas páginas.

- Mueve la ventana de page2 muy lentamente. Observa la consola de page2. ¿Cuándo aparece el mensaje "Page 2 detected change..."?

- Deja la ventana de page2 quieta. ¿Aparece el mensaje?

- ¿Por qué es eficiente esta estrategia de comparar con el estado anterior antes de enviar datos por la red? Anota tu reflexión.
:::

##### 5. La visualización (draw)

``` js
function draw() {
    background(220);
    drawCircle(point2[0], point2[1]); // Círculo en el centro de esta ventana
    checkWindowPosition(); 

    // Calcula la posición relativa de la otra ventana
    let vector1 = createVector(currentPageData.x, currentPageData.y); 
    let vector2 = createVector(remotePageData.x, remotePageData.y); 
    let resultingVector = createVector(vector2.x - vector1.x, vector2.y - vector1.y); 

    stroke(50);
    strokeWeight(20);
    // Dibuja dónde estaría el centro de la otra ventana RELATIVO a esta
    drawCircle(resultingVector.x + remotePageData.width / 2, resultingVector.y + remotePageData.height / 2); 
    // Línea conectando los centros (uno local, otro relativo)
    line(point2[0], point2[1], resultingVector.x + remotePageData.width / 2, resultingVector.y + remotePageData.height / 2);
}

function drawCircle(x, y) { /* ... dibuja el círculo ... */ }
```

:::note[🧩 **Explicación**]

La función draw() de p5.js se ejecuta continuamente (unos 60 frames por segundo).

Limpia el fondo, dibuja el círculo local en point2.

Llama a checkWindowPosition() para enviar actualizaciones si es necesario.

Cálculo de Posición Relativa: crea vectores p5.js para la posición de esta ventana (vector1) y 
la ventana remota (vector2, usando remotePageData que se actualizó por Socket.IO). Restar vector1 
de vector2 nos da un vector (resultingVector) que apunta desde nuestra esquina superior izquierda a 
la esquina superior izquierda de la otra ventana.

Dibujo Relativo: usa resultingVector para calcular dónde dibujar el círculo que representa el 
centro de la ventana remota. Se le suma la mitad del ancho/alto de la ventana remota 
(remotePageData.width / 2, remotePageData.height / 2) para apuntar a su centro.

Finalmente, dibuja una línea entre nuestro centro (point2) y el centro relativo calculado 
de la otra ventana.
:::


:::caution[🧐🧪✍️ **Experimenta** (¡Sé creativo!)]

- Cambia el background(220) para que dependa de la distancia entre las ventanas. Puedes calcular la 
magnitud del resultingVector usando let distancia = resultingVector.mag(); y luego usar map() para 
convertir esa distancia a un valor de gris o color. background(map(distancia, 0, 1000, 255, 0)); 
(ajusta el rango 0-1000 según sea necesario).

En lugar de dibujar el segundo círculo, haz que el tamaño del círculo local 
(drawCircle(point2[0], point2[1])) dependa del ancho de la ventana remota 
(remotePageData.width). Tendrás que modificar la función drawCircle para aceptar un 
tamaño o cambiar el tamaño fijo (150, 150) directamente en draw().

Intenta cambiar el color de la línea (stroke(...)) basado en si la ventana remota está a 
la izquierda o derecha de la actual (if (resultingVector.x < 0) { stroke(...) } else { stroke(...) }).

¡Combina ideas o inventa tu propia visualización! Anota qué intentaste y qué resultado obtuviste.
:::

:::caution[📤 **Entrega**]
Completa tu bitácora con las respuestas, observaciones y reflexiones de los experimentos 
de la parte del cliente. Asegúrate de entender cómo el cliente envía datos (emit), recibe datos (on), 
detecta cambios y usa los datos recibidos para la visualización.
:::

