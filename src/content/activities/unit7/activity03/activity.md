#### Análisis del servidor puente (`server.js`)

:::note[🎯 Enunciado]
Vamos a analizar el código `server.js`. Este script actúa como un repetidor simple 
pero esencial, recibiendo mensajes del cliente móvil y retransmitiéndolos al cliente de escritorio.
:::

:::tip[Recursos]
- El archivo `server.js` del caso de estudio.
- Tus notas de la Unidad 6 sobre Node.js, Express y Socket.IO (si necesitas un repaso básico).
- La explicación de Dev Tunnels y JSON de la actividad anterior.
:::

👣 **Pasos**: (Análisis del código)

``` js
// server.js
const express = require('express'); // Framework para servidor web
const http = require('http');       // Módulo HTTP base de Node.js
const socketIO = require('socket.io'); // Librería para WebSockets

const app = express();             // Crea la aplicación Express
const server = http.createServer(app); // Crea el servidor HTTP usando Express
const io = socketIO(server);       // Vincula Socket.IO al servidor HTTP
const port = 3000;                 // Puerto en el que escuchará LOCALMENTE
```

:::note[🧩 Explicación (Setup inicial)]

Importamos las librerías necesarias: express para facilitar la gestión del servidor web, http para crear el servidor base, y socket.io para la comunicación en tiempo real.

Creamos la aplicación express (app).

Creamos el servidor http y le decimos que use app para manejar las peticiones.

Creamos la instancia de socket.io (io) y la conectamos a nuestro servidor http. Ahora io gestionará las conexiones WebSocket.

Definimos el puerto 3000 donde el servidor escuchará en la máquina local (Dev Tunnels se encargará de exponerlo).
:::

``` js
// Sirve archivos estáticos desde la carpeta 'public'
app.use(express.static('public'));

```
:::note[🧩 Explicación (Servir archivos cliente)]

app.use(express.static('public')) es una configuración clave de Express.

Le dice al servidor: "Si un navegador pide un archivo (como /desktop/index.html, /desktop/sketch.js, /mobile/index.html, etc.), búscalo dentro de la carpeta public y envíalo directamente."

Esto es más simple que definir rutas separadas (app.get) para cada archivo HTML como en el ejemplo de la Unidad 6, ya que sirve todo el contenido de public automáticamente según la URL solicitada. Por ejemplo, una petición a <url>/desktop/ buscará public/desktop/index.html por defecto.
:::

``` js
// Evento: se dispara cuando un nuevo cliente (navegador) se conecta vía Socket.IO
io.on('connection', (socket) => {
    console.log('New client connected - ID:', socket.id);

    // Evento: se dispara cuando ESE cliente envía un mensaje llamado 'message'
    socket.on('message', (message) => {
        console.log(`Received message => ${message} from ID: ${socket.id}`);
        // Retransmite el mensaje recibido a TODOS los OTROS clientes conectados
        socket.broadcast.emit('message', message);
    });

    // Evento: se dispara cuando ESE cliente se desconecta
    socket.on('disconnect', () => {
        console.log('Client disconnected - ID:', socket.id);
    });
});
```

:::note[🧩 Explicación (Manejo de conexiones y mensajes)]

* io.on('connection', (socket) => { ... }); es el corazón de Socket.IO en el servidor. Esta función se ejecuta cada vez que un cliente (sea el escritorio o el móvil) establece una conexión WebSocket.

* El parámetro socket representa la conexión individual con ese cliente específico. Cada cliente conectado tiene su propio objeto socket.

* console.log('New client connected...'): informa en la consola del servidor que alguien se conectó, mostrando su socket.id único.

* socket.on('message', (message) => { ... });: dentro del manejador de connection, definimos qué hacer cuando ese cliente específico (socket) envía un evento llamado 'message'.

* message: contiene los datos enviados por el cliente (en nuestro caso, la cadena JSON con los datos del touch).

* console.log(...): muestra el mensaje recibido y quién lo envió.

* socket.broadcast.emit('message', message);: ¡La retransmisión!

    * broadcast: significa "enviar a todos los clientes conectados, excepto al que originó este mensaje (socket)".

    * emit('message', message): envía un evento 'message' con los mismos datos (message) que se recibieron.

    * Efecto: cuando el móvil envía un mensaje, el servidor lo recibe y lo reenvía inmediatamente al escritorio (y a cualquier otro cliente conectado, excepto al propio móvil).

* socket.on('disconnect', () => { ... });: se ejecuta cuando ese cliente específico cierra la conexión (cierra la pestaña, pierde Internet, etc.). Informa en la consola.
:::

``` js
// Inicia el servidor y lo pone a escuchar en el puerto local especificado
server.listen(port, () => {
    console.log(`Server is listening on http://localhost:${port}`);
});
```

:::note[🧩 Explicación (Inicio del servidor)]

server.listen(port, ...): le dice al servidor HTTP que comience a aceptar conexiones en el port (3000) definido localmente.

La función () => { ... } es un callback que se ejecuta una vez que el servidor se ha iniciado correctamente. Imprime el mensaje confirmando que está escuchando localmente (recordando que Dev Tunnels lo hace accesible externamente).
:::

:::note[🧐🧪✍️ Reporta en tu bitácora]

¿Cuál es la función principal de express.static('public') en este servidor? ¿Cómo se compara con el uso de app.get('/ruta', ...) del servidor de la Unidad 6?

Explica detalladamente el flujo de un mensaje táctil: ¿Qué evento lo envía desde el móvil? ¿Qué evento lo recibe el servidor? ¿Qué hace el servidor con él? ¿Qué evento lo envía el servidor al escritorio? ¿Por qué se usa socket.broadcast.emit en lugar de io.emit o socket.emit en este caso?

Si conectaras dos computadores de escritorio y un móvil a este servidor, y movieras el dedo en el móvil, ¿Quién recibiría el mensaje retransmitido por el servidor? ¿Por qué?

¿Qué información útil te proporcionan los mensajes console.log en el servidor durante la ejecución?
:::

:::caution[📤 Entrega]
Documenta en tu bitácora el análisis del server.js, explicando cada sección principal y respondiendo a las preguntas de reflexión sobre su funcionamiento y el flujo de mensajes.
:::

