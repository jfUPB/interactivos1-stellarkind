## Solución a la quinta actividad
**Mi idea:** Me gustaría expandir en el caso de estudio conectado tres ventanas del navegador en tiempo real. Cada ventana mostrará la posición de las tres ventanas como si fuesen estrellas (cambiaré los círculos por estrellas para hacer analogía a la conexión como constelaciones), me gustaría también que cuado cualquiera de las tres ventanas se mueva, esa ventna se convierta en el centro de la constelación (o pues la estrella que une a las otras dos), así entonces las otras ventanas que no se movieron también se actualizarán para coincidir visualmente con la ventana que sí se movió.

**Info que debería tener en cuenta:** Cada uno de los clientes necesitará enviar su tamaño y posición al servidor. El servidor necesitaría conocer la posición y tamaño de todas las vetanas conectadas y retransmitir esa info a todos los clientes, incluyendo el dato de cuál fue la ventana que envió la última actualización de forma que esta se convierta en el eje de la constelación y todas las ventanas se actualicen visualmente de forma adecuada.

Mi plan de implementación:
1. Para el servidor (```server.js```):
     - Toca modificar para rastrear ya no solo los datos de dos sino de tres clientes. Puede ser util para eso utilizar un objeto para guardar la info de cada cliente identificandolos con su ```socket.id```, de manera que el objeto almacene a qué página perenece cada ```socket.id```.
     - Toca crear una nueva ruta ```/page3``` que sirva ```page3.html```.
     - Puede ser util reemplazar los eventos de cada ventana(```win1/2update```) por un evento general que cualquier cliente pueda enviar con sus datos actuales.
     - Añadir un evento para que cada cliente se identifique (```page1```, ```page2``` o ```page3```), lo que ayudaría a mapear el ```socket.id``` según su tipo de página.
     - Hay que transmitir a todos los clietes un mensaje que contenga el estado actual de todos los clientes conectados, que incluya el ```socket.id``` del cliente qe envió la última actualización.
  2. Para los clientes (```page1```, ```page2```, ```page3```):
     - Hay que crear los archivos ```page3.html``` y ```page3.js```.
     - Cada cliente mantendrá su propio estado (```currentPageData```) y calculará su centro (```myPoint```).
     - Cada cliente debería mantener un almacenamiento local para los datos de todos los clientes, que se actualice por los mensajes del servidor.
     - Debería modificar el código de conexión para qu mande un mensage de registro (```'register page'```) al conectarse que indique si es la página 1, 2 o 3.
     - Toca modificar también el código de ```checkWindowPosition``` para emitir el evento ```'page_update'``` en lugar de ```win1/2update```.
     - Toca modificar el oyente de recepción de datos del servidor (antes ```'getdata'```, ahora sería ```'all_pages_data'```) para actualizar el almacenamiento de los datos de todos los clientes (```allClientData```) y el ID del último emisor (```lastUpdatedSenderId```).
     - Para modificar en la funció ```draw()```:
       - Limpiar el fondo.
       - Iterar sobre los datos de todos los clientes en ```allClientData```, ara dibujar una estrella en la posición relativa de cada ventana. El color de la estrella podría indicar si es la ventana pivote, o las otras.
       - Usar ```lastUpdatedSenderId``` para identificar la ventana pivote. Si hay una ventana pivote identificada, dibujar líneas desde el centro de la ventana pivote (en su posición relativa) hacia los centros de las otras dos ventanas (en sus posiciones relativas). Esto requiere calcular la posición relativa de cada ventana respecto a mi ventana y luego dibujar líneas entre esas posiciones relativas.
       - Implementar una función ```drawStar``` para dibujar estrellas en lugar de círculos.

### Código de mi aplicación:
```index.html```: (No necesita cambios, ya que server.js servirá page1.html, page2.html, page3.html y cada uno cargará su JS correspondiente).

```page1.html```, ```page2.html```, ```page3.html```: (Son idénticos excepto por el título y el archivo .js que cargan).

```page1.html```:
``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Constellation Page 1</title>
    <script src="https://cdn.jsdelivr.net/npm/p5@1.11.0/lib/p5.min.js"></script>
    <script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #1a1a2e; /* Fondo oscuro para constelaciones */
        }
    </style>
</head>
<body>
    <script defer src="page1.js"></script>
</body>
</html>
```
```page2.html```: (Igual que ```page1.html```, cambiando ```<title>Page 2</title>``` y ```<script defer src="page2.js"></script>```)
```page3.html```: (Igual que ```page1.html```, cambiando ```<title>Page 3</title>``` y ```<script defer src="page3.js"></script>```)

```server.js```:
``` js
const express = require('express');
const http = require('http');
const socketIO = require('socket.io');
const path = require('path');

const app = express();
const server = http.createServer(app);
const io = socketIO(server);
const port = 3000;

// Almacena los datos de todos los clientes conectados, indexados por socket.id
// { socket.id: { x, y, width, height, online, pageNumber }, ... }
let clientsData = {};

// Mapea socket.id a pageNumber ('page1', 'page2', 'page3')
let pageMap = {};

// Sirve archivos estáticos desde la carpeta 'views'
app.use(express.static(path.join(__dirname, 'views')));

// Define las rutas para servir los archivos HTML de cada página
app.get('/page1', (req, res) => {
    res.sendFile(path.join(__dirname, 'views', 'page1.html'));
});

app.get('/page2', (req, res) => {
    res.sendFile(path.join(__dirname, 'views', 'page2.html'));
});

app.get('/page3', (req, res) => {
    res.sendFile(path.join(__dirname, 'views', 'page3.html'));
});

// Evento principal de Socket.IO: se dispara cuando un nuevo cliente se conecta
io.on('connection', (socket) => {
    console.log('A user connected - ID:', socket.id);

    // Maneja la desconexión de un cliente
    socket.on('disconnect', () => {
        console.log('User disconnected - ID:', socket.id);
        // Marca al cliente como offline y elimina el mapeo de página
        if (clientsData[socket.id]) {
             clientsData[socket.id].online = false; // Marca como offline
             // Opcional: Eliminar completamente si no se necesita rastrear clientes offline
             // delete clientsData[socket.id];
        }
         if (pageMap[socket.id]) {
            delete pageMap[socket.id];
         }

        // Broadcast el estado actualizado a todos (incluyendo el que se fue, aunque no lo recibirá)
        // Esto permite que los otros clientes sepan que una página se desconectó
         io.emit('all_pages_data', { senderId: socket.id, data: clientsData });
    });

    // Maneja el registro de una página (cliente informa si es page1, page2, o page3)
    // El cliente envía { pageNumber: 'pageX', initialData: { x, y, width, height } }
    socket.on('register_page', (payload) => {
        const pageNumber = payload.pageNumber;
        const initialData = payload.initialData;

        if (!pageNumber || !initialData) {
             console.warn(`Registration failed for ID ${socket.id}: Missing pageNumber or initialData`);
             return; // Ignorar registro inválido
        }

        console.log(`Socket ${socket.id} registered as ${pageNumber}`);
        pageMap[socket.id] = pageNumber;
        // Almacena los datos iniciales del cliente con estado online: true
        clientsData[socket.id] = { ...initialData, online: true, pageNumber: pageNumber };

        // Envía el estado actual de TODOS los clientes conectados al cliente que acaba de conectar
        socket.emit('all_pages_data', { senderId: null, data: clientsData }); // senderId es null para la carga inicial

        // Broadcast el estado actualizado a todos (incluyendo al que acaba de conectar)
        // Esto asegura que todos los clientes sepan que una nueva página está ahora online
        io.emit('all_pages_data', { senderId: socket.id, data: clientsData }); // Usa el ID del nuevo cliente como emisor inicial del broadcast
    });

    // Maneja las actualizaciones de posición/tamaño enviadas por los clientes
    // El cliente envía { x, y, width, height }
    socket.on('page_update', (pageData) => {
        const senderId = socket.id; // El ID del cliente que envió la actualización
        // Verifica si este cliente está registrado
        if (!pageMap[senderId]) {
             console.warn(`Received update from unregistered ID: ${senderId}. Ignoring.`);
             return; // Ignorar actualización si el cliente no se registró primero
        }

        // Actualiza los datos de este cliente en el almacenamiento global
        // Mantiene pageNumber y asegura online: true
        clientsData[senderId] = { ...pageData, online: true, pageNumber: pageMap[senderId] };

        // Broadcast el estado actualizado de TODOS los clientes a todos los clientes conectados,
        // incluyendo el ID del cliente que envió la actualización para identificar el "pivote".
        io.emit('all_pages_data', { senderId: senderId, data: clientsData });
    });

    // Optional: Manejar otros eventos si se añaden en el futuro
});

// Inicia el servidor y lo pone a escuchar en el puerto especificado
server.listen(port, () => {
    console.log(`Server is listening on http://localhost:${port}`);
});
```
```page1.js```: (Los archivos ```page2.js``` y ```page3.js``` son casi idénticos, cambiando solo el valor de ```myPageId```)  
``` js
// page1.js (Cliente para la Constelación Interactiva)

// Variables para almacenar la información de mi ventana y todas las ventanas conectadas
let currentPageData; // Información de posición y tamaño de esta ventana
let myPoint; // Vector p5.js para el centro de esta ventana
let previousPageData = { x: 0, y: 0, width: 0, height: 0 }; // Para detectar cambios locales

// Almacena los datos de todos los clientes conectados, indexados por socket.id
// { socket.id: { x, y, width, height, online, pageNumber }, ... }
let allClientData = {};

// Identificador de esta página para el registro en el servidor
const myPageId = 'page1'; // Cambiar a 'page2' en page2.js, 'page3' en page3.js
let myId = null; // Para guardar mi propio socket.id una vez conectado

// ID del cliente que envió la última actualización (el "pivote")
let lastUpdatedSenderId = null;

// Conexión al servidor Socket.IO
let socket = io('http://localhost:3000');


// --- Funciones de P5.js ---

function setup() {
    createCanvas(windowWidth, windowHeight); // Crea el lienzo que ocupa la ventana
    background('#1a1a2e'); // Fondo oscuro inicial

    // Captura la información inicial de la ventana
    currentPageData = {
        x: window.screenX,
        y: window.screenY,
        width: window.innerWidth,
        height: window.innerHeight
    };
    // Inicializa previousPageData para que no envíe una actualización inmediata al inicio
    previousPageData = {
        x: currentPageData.x,
        y: currentPageData.y,
        width: currentPageData.width,
        height: currentPageData.height
    };
    // Calcula la posición inicial del centro de esta ventana
    myPoint = createVector(currentPageData.width / 2, currentPageData.height / 2);


    // --- Manejadores de Eventos de Socket.IO ---

    // Se dispara cuando la conexión con el servidor se establece
    socket.on('connect', () => {
        myId = socket.id; // Guarda mi propio ID asignado por el servidor
        console.log('Connected to server - My ID:', myId);
        // Regístra esta página con el servidor, enviando su tipo y datos iniciales
        socket.emit('register_page', { pageNumber: myPageId, initialData: currentPageData });
    });

    // Se dispara al recibir el estado inicial de todos los clientes del servidor
    // (Ocurre al conectar y potencialmente en otras situaciones que el servidor decida)
    socket.on('all_pages_data', (payload) => {
        // Actualiza el almacenamiento local con los datos recibidos de todos los clientes
        allClientData = payload.data;
        // Guarda el ID del cliente que envió la última actualización (el pivote)
        lastUpdatedSenderId = payload.senderId;

        console.log('Received all_pages_data. Pivot ID:', lastUpdatedSenderId, 'All data:', allClientData);
    });


    // Se dispara cuando se desconecta del servidor
    socket.on('disconnect', () => {
        console.log('Disconnected from server');
        // Opcional: Limpiar allClientData o marcar clientes como offline visualmente
        // allClientData = {}; // Opcional: Limpiar datos al desconectar
    });

    // Nota: El oyente 'page_state_update' del plan anterior se combinó en 'all_pages_data' para simplificar el flujo.
    // El servidor envía todo el estado en cada actualización significativa.
}


function draw() {
    background('#1a1a2e'); // Fondo oscuro para la visualización de constelaciones

    // Dibuja las estrellas y líneas solo si hay al menos un cliente conectado (incluyéndome)
    // y tenemos datos de clientes.
    const connectedClientsCount = Object.values(allClientData).filter(client => client.online).length;

    if (connectedClientsCount > 0) {
        let myPageData = allClientData[myId]; // Obtiene mis propios datos del almacenamiento global

        // Asegura que mis datos estén disponibles antes de dibujar (puede tomar un frame después de conectar)
        if (myPageData) {

             // --- Dibuja las estrellas para cada ventana ---
             stroke(200); // Borde gris claro para las estrellas
             strokeWeight(1);
             for (const [id, data] of Object.entries(allClientData)) {
                 if (data.online) { // Solo dibuja si el cliente está marcado como online
                     // Calcula la posición de esta ventana RELATIVA a mi ventana actual
                     let relativePos = createVector(data.x - currentPageData.x, data.y - currentPageData.y);

                     // Calcula el centro de la estrella
                     let starX = relativePos.x + data.width / 2;
                     let starY = relativePos.y + data.height / 2;

                     // Decide el color de la estrella: mía, la pivote, u otra
                     if (id === myId) {
                         fill(0, 255, 0); // Verde para mi estrella
                     } else if (id === lastUpdatedSenderId) {
                         fill(255, 0, 0); // Rojo para la estrella pivote
                     } else {
                         fill(0, 0, 255); // Azul para las otras estrellas
                     }

                     // Dibuja la estrella
                     drawStar(starX, starY, 20, 8, 5); // Estrella de 5 puntas
                 }
             }

             // --- Dibuja las líneas de conexión desde el pivote ---
             // Solo dibuja líneas si hay un pivote identificado y al menos 2 clientes online además del pivote.
             // O si el pivote soy yo y hay al menos 2 clientes online en total.
             if (lastUpdatedSenderId && allClientData[lastUpdatedSenderId] && connectedClientsCount >= 2) {

                 let pivotData = allClientData[lastUpdatedSenderId]; // Datos de la ventana pivote
                 // Calcula la posición de la ventana pivote RELATIVA a mi ventana
                 let pivotRelativePos = createVector(pivotData.x - currentPageData.x, pivotData.y - currentPageData.y);
                 // Calcula el centro relativo de la ventana pivote
                 let pivotCenterX = pivotRelativePos.x + pivotData.width / 2;
                 let pivotCenterY = pivotRelativePos.y + pivotData.height / 2;

                 stroke(255, 165, 0, 150); // Color de las líneas (naranja semitransparente)
                 strokeWeight(2); // Grosor de las líneas

                 // Itera sobre todos los clientes para dibujar líneas desde el pivote a los OTROS clientes
                 for (const [id, data] of Object.entries(allClientData)) {
                     if (data.online && id !== lastUpdatedSenderId) { // Si el cliente está online y NO es el pivote
                         // Calcula la posición de este otro cliente RELATIVA a mi ventana
                         let otherRelativePos = createVector(data.x - currentPageData.x, data.y - currentPageData.y);
                         // Calcula el centro relativo de este otro cliente
                         let otherCenterX = otherRelativePos.x + data.width / 2;
                         let otherCenterY = otherRelativePos.y + data.height / 2;

                         // Dibuja una línea desde el centro relativo del pivote al centro relativo de este otro cliente
                         line(pivotCenterX, pivotCenterY, otherCenterX, otherCenterY);
                     }
                 }
             }
        } else {
             // Si mis datos aún no están disponibles (justo al conectar antes de recibir allClientData)
             // Podría dibujar un mensaje de "Conectando..." o simplemente dejar el fondo oscuro.
        }
    } else {
         // Si no hay clientes online (solo yo, o nadie)
         // Podría dibujar un mensaje de "Conecta otras ventanas"
          textAlign(CENTER, CENTER);
          textSize(20);
          fill(200);
          noStroke();
          text("Open http://localhost:3000/page1, /page2, and /page3 in other tabs", width / 2, height / 2);
    }


    // --- Verifica y Envía Actualizaciones Locales ---
    // Esta función detecta si mi ventana se movió o redimensionó y envía la actualización al servidor.
    checkWindowPosition();
}


function checkWindowPosition() {
    // Obtiene la información actual de la ventana
    currentPageData = {
        x: window.screenX,
        y: window.screenY,
        width: window.innerWidth,
        height: window.innerHeight
    };

    // Calcula el centro actual de mi ventana (necesario si la ventana se redimensiona)
    myPoint = createVector(currentPageData.width / 2, currentPageData.height / 2);

    // Compara el estado actual con el estado anterior para ver si hubo cambios significativos
    if (currentPageData.x !== previousPageData.x || currentPageData.y !== previousPageData.y ||
        currentPageData.width !== previousPageData.width || currentPageData.height !== previousPageData.height) {

        // console.log(`Page ${myPageId} detected change, sending update:`, currentPageData); // Log

        // Si hubo cambios, envía el estado actual de esta ventana al servidor
        // Emite el evento 'page_update' con los datos de mi ventana
        socket.emit('page_update', currentPageData);

        // Actualiza el estado anterior para la próxima comparación
        previousPageData = {
            x: currentPageData.x,
            y: currentPageData.y,
            width: currentPageData.width,
            height: currentPageData.height
        };
    }
}

// Función para dibujar una estrella (similar a la de la Actividad 01, Unidad 5)
function drawStar(cx, cy, outerRadius, innerRadius, points) {
  let angle = TWO_PI / points; // Ángulo entre las puntas
  let halfAngle = angle / 2.0; // Medio ángulo

  beginShape(); // Comienza a definir la forma de la estrella
  for (let a = 0; a < TWO_PI; a += angle) {
    // Vértice exterior
    let sx = cx + cos(a - HALF_PI) * outerRadius; // Calcula la coordenada X del vértice exterior
    let sy = cy + sin(a - HALF_PI) * outerRadius; // Calcula la coordenada Y del vértice exterior
    vertex(sx, sy); // Añade el vértice exterior a la forma

    // Vértice interior
    sx = cx + cos(a + halfAngle - HALF_PI) * innerRadius; // Calcula la coordenada X del vértice interior
    sy = cy + sin(a + halfAngle - HALF_PI) * innerRadius; // Calcula la coordenada Y del vértice interior
    vertex(sx, sy); // Añade el vértice interior a la forma
  }
  endShape(CLOSE); // Termina la forma y la cierra (conecta el último vértice con el primero)
}

// Maneja el redimensionamiento de la ventana del navegador
function windowResized() {
  resizeCanvas(windowWidth, windowHeight); // Redimensiona el lienzo de p5.js
  // Vuelve a capturar los datos actuales y actualiza myPoint después de redimensionar
  currentPageData = {
    x: window.screenX,
    y: window.screenY,
    width: window.innerWidth,
    height: window.innerHeight
  };
  myPoint = createVector(currentPageData.width / 2, currentPageData.height / 2);
  // También es bueno enviar una actualización al servidor después de redimensionar
  // Asegura que el servidor y otros clientes conozcan el nuevo tamaño/posición.
   socket.emit('page_update', currentPageData);
}
```
```page2.js```: (el código de ```page1.js``` cambia de ```const myPageId = 'page1';``` a ```const myPageId = 'page2';```)  
```page3.js```: (el código de ```page1.js``` cambia de ```const myPageId = 'page1';``` a ```const myPageId = 'page3';```)  
### Reflexión sobre el Proceso de Modificación:

#### ¿Qué fue fácil?
Fue relativamente fácil adaptar la estructura existente de dos clientes a tres, una vez que entendí cómo el servidor manejaba los mensajes de uno y hacía broadcast al otro. La lógica de checkWindowPosition para detectar cambios locales también fue sencilla de reutilizar. Cambiar los círculos por estrellas fue fácil una vez que implementé la función drawStar.

#### ¿Qué fue difícil?
Lo más desafiante fue implementar la lógica de visualización para la parte de las líneas, específicamente que la ventana que se mueve se convierta en el "eje". Requirió pensar en cómo rastrear la ventana que generó la última actualización (```lastUpdatedSenderId```) y luego cómo calcular las posiciones relativas de todas las ventanas desde la perspectiva de mi ventana actual para poder dibujar las líneas saliendo del centro relativo de la ventana pivote hacia los otros centros relativos. Depurar la lógica de la función ```draw()``` y asegurarse de que las líneas se dibujaran correctamente entre los centros relativos de las tres ventanas (identificando el pivote) tomó tiempo, y al final no pude implementarlo exactamente como me lo imaginaba porque la función ```draw()``` de page3 por alguna razón no se actualiza correctamente reflejando la posición de su estrella :c

[Video de mi aplicación](https://drive.google.com/file/d/1JZoEeY-4EUjTYyZ-wtIwmOI4prvughen/view?usp=drive_link) 

Aprendí la importancia de tener una estructura de datos clara en el servidor que represente el "estado global" de la aplicación (en este caso, la posición de todas las ventanas). Aprendí que la comunicación en tiempo real implica no solo enviar mensajes, sino también gestionar ese estado global y asegurarse de que todos los clientes estén sincronizados. 
