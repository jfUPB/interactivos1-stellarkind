#### El Servidor (Node.js)

:::note[🎯 **Enunciado**]
Vamos a analizar paso a paso el archivo server.js. Este script es el núcleo que 
permite la comunicación en tiempo real. No te preocupes si ves código nuevo, iremos explicando cada parte.
:::

##### 1. Dependencias: las herramientas necesarias

``` js
// server.js
const express = require('express');
const http = require('http');
const socketIO = require('socket.io');
const path = require('path');
```

:::note[🧩 **Explicación**]
Estas líneas importan módulos (librerías) que Node.js necesita.

* express: es un framework popular para construir servidores web de forma más sencilla. Nos ayuda a manejar las peticiones de los navegadores.

* http: es el módulo base de Node.js para crear servidores HTTP. Express lo usa por debajo.

* socket.io: ¡La estrella de la comunicación en tiempo real! Permite enviar mensajes bidireccionales instantáneos entre el servidor y los clientes (navegadores).

* path: una utilidad para trabajar con rutas de archivos y directorios de forma segura en cualquier sistema operativo.
:::

:::caution[🧐✍️ Reflexiona]
¿Por qué crees que es útil usar módulos o librerías en lugar de escribir todo desde cero? ¿Qué ventajas aporta? Anota tus ideas en la bitácora.
:::

##### 2. Creación del Servidor y Socket.IO

``` js
const app = express();
const server = http.createServer(app); 
const io = socketIO(server); 
const port = 3000;
```

:::note[🧩 **Explicación**]

* app = express(): creamos una instancia de la aplicación Express. app será nuestro objeto principal para configurar el servidor web.

* server = http.createServer(app): creamos un servidor HTTP estándar y le decimos que use nuestra aplicación app de Express para gestionar las peticiones.

* io = socketIO(server): ¡Aquí conectamos Socket.IO! Le decimos que ``"escuche"`` en nuestro servidor HTTP (server). Ahora io nos permitirá manejar las conexiones de Socket.IO.

* port = 3000: definimos la variable port con el número 3000. Es el número de puerto en el que nuestro servidor escuchará las conexiones entrantes.
:::

##### 3. Variables para guardar el estado

``` js
let page1 = { x: 0, y: 0, width: 100, height: 100 };
let page2 = { x: 0, y: 0, width: 100, height: 100 };
```

:::note[🧩 **Explicación**]
Estas variables globales (page1, page2) las usará el servidor para recordar la 
última información (posición y tamaño) que recibió de cada cliente (ventana del navegador). 
Se inicializan con valores por defecto.
:::

##### 4. Sirviendo los archivos del cliente

``` js	
// Sirve archivos estáticos (HTML, CSS, JS del cliente) desde la carpeta 'views'
app.use(express.static(path.join(__dirname, 'views')));
```

:::note[🧩 **Explicación**]
Esta línea es importante. Le dice a Express que, si un navegador pide un archivo 
(como page1.js o style.css), lo busque dentro de la carpeta views. ``__dirname`` es una variable 
especial de Node.js que representa la ruta absoluta de la carpeta donde se encuentra 
server.js, y path.join la une de forma segura con views.
:::

:::caution[🧐🧪✍️ Experimenta]

- Detén el servidor (Ctrl+C en la terminal).

- Cambia la línea a app.use(express.static(path.join(__dirname, 'archivos_cliente'))); 
(usa un nombre de carpeta que no exista).

- Inicia el servidor (npm start) y trata de abrir http://localhost:3000/page1.html 
en el navegador.

- ¿Qué ocurre? ¿Por qué? ¿Qué mensaje ves en el navegador o en su consola 
de desarrollador (F12)?

- Vuelve a dejar la línea como estaba ('views') y verifica que funcione de nuevo. Anota tus observaciones.
:::


##### 5. Rutas: Qué enviar cuando se pide una URL

``` js
// Define la ruta para servir page1.html
app.get('/page1', (req, res) => {
    res.sendFile(path.join(__dirname, 'views', 'page1.html'));
});

// Define la ruta para servir page2.html
app.get('/page2', (req, res) => {
    res.sendFile(path.join(__dirname, 'views', 'page2.html'));
});
```

:::note[🧩 **Explicación**] 
Estas funciones definen qué debe hacer el servidor cuando un navegador solicita las URLs /page1 o /page2. app.get le dice a Express que escuche peticiones GET para esas rutas. Cuando llega una petición, ejecuta la función asociada:

res.sendFile(...): Envía el archivo HTML correspondiente (page1.html o page2.html) de vuelta al navegador. De nuevo, path.join construye la ruta completa al archivo.
:::

:::caution[🧐🧪✍️ Experimenta]

- Detén el servidor.

- Cambia la primera ruta de /page1 a /pagina_uno.

- Inicia el servidor.

- Intenta acceder a http://localhost:3000/page1. ¿Funciona?

- Ahora intenta acceder a http://localhost:3000/pagina_uno. ¿Funciona?

- ¿Qué te dice esto sobre cómo el servidor asocia URLs con respuestas? Restaura el código.
:::


##### 6. ¡La Magia de Socket.IO! La Conexión

``` js
// Evento principal de Socket.IO: se dispara cuando un cliente se conecta
io.on('connection', (socket) => {
    console.log('A user connected - ID:', socket.id); // Muestra el ID único del cliente conectado

    // (Aquí dentro irán los manejadores de eventos para ESE cliente)

    // Evento: se dispara cuando ESE cliente se desconecta
    socket.on('disconnect', () => {
        console.log('User disconnected - ID:', socket.id);
    });
});
```

:::note[🧩 **Explicación**] 
Esta es la parte central de Socket.IO en el servidor.

io.on('connection', ...): Establece un ``"oyente"`` para el evento connection. Este evento 
se dispara cada vez que un nuevo cliente (una ventana del navegador con page1.js o page2.js) 
establece una conexión de Socket.IO con el servidor.

La función que se ejecuta recibe un parámetro importantísimo: socket. Este objeto socket 
representa la conexión individual con ese cliente específico. Cada cliente tendrá su 
propio objeto socket.

socket.id: es un identificador único que Socket.IO asigna a cada conexión. Útil para saber 
quién se conecta o desconecta.

socket.on('disconnect', ...): dentro del oyente de connection, establecemos otro oyente 
específico para ese socket. Este se dispara cuando ese cliente en particular se desconecta 
(cierra la pestaña, pierde conexión, etc.).
:::


:::caution[🧐🧪✍️ **Experimenta**]

- Asegúrate de que el servidor esté corriendo (npm start).

- Abre http://localhost:3000/page1 en una pestaña. Observa la terminal del servidor. ¿Qué mensaje ves? Anota el ID.

- Abre http://localhost:3000/page2 en OTRA pestaña. Observa la terminal. ¿Qué mensaje ves? ¿El ID es diferente?

- Cierra la pestaña de page1. Observa la terminal. ¿Qué mensaje ves? ¿Coincide el ID con el que anotaste?

- Cierra la pestaña de page2. Observa la terminal.
:::


##### 7. Escuchando Mensajes de los Clientes

``` js
// Dentro de io.on('connection', (socket) => { ... });

    // Evento: escucha mensajes 'win1update' enviados desde page1.js
    socket.on('win1update', (window1, sendid) => {
        console.log('Received win1update from ID:', socket.id, 'Data:', window1); // Log para ver qué llega
        page1 = window1; // Actualiza la información del estado de page1 en el servidor
        
        // Envía la información actualizada de page1 a TODOS los demás clientes conectados
        socket.broadcast.emit('getdata', page1); 
    });

    // Evento: escucha mensajes 'win2update' enviados desde page2.js
    socket.on('win2update', (window2, sendid) => {
        console.log('Received win2update from ID:', socket.id, 'Data:', window2); // Log para ver qué llega
        page2 = window2; // Actualiza la información del estado de page2 en el servidor
        
        // Envía la información actualizada de page2 a TODOS los demás clientes conectados
        socket.broadcast.emit('getdata', page2);
    });
```

:::note[🧩 **Explicación**] 
Estos bloques también van dentro de io.on('connection', ...) 
porque definen cómo debe reaccionar el servidor a mensajes de un cliente 
específico (socket).

socket.on('win1update', ...): escucha un evento llamado 'win1update'. 
Esperamos que el cliente page1.js envíe este tipo de mensaje. 
Cuando llega, se ejecuta la función.

window1, sendid: son los datos que el cliente envía junto con el evento (veremos esto en page1.js). 
window1 contiene la información de posición/tamaño.

console.log(...): añadimos un log para ver qué datos llegan y de quién.

page1 = window1;: actualizamos la variable global del servidor con los datos frescos 
recibidos de page1.

socket.broadcast.emit('getdata', page1);: ¡La retransmisión!

broadcast: significa "a todos menos a mí" (al socket que envió el mensaje original).

emit('getdata', page1): envía un nuevo mensaje llamado 'getdata' a todos los demás 
clientes, llevando consigo la información actualizada de page1.

El bloque socket.on('win2update', ...) es simétrico, pero para los mensajes que vienen de page2.js.
:::

:::caution[🧐🧪✍️ **Experimenta**]

- Asegúrate de que los console.log que añadimos estén presentes en tu server.js.

- Inicia el servidor y abre page1 y page2.

- Mueve la ventana de page1. Observa la terminal del servidor. ¿Qué evento se registra 
(win1update o win2update)? ¿Qué datos (Data:) ves?

- Mueve la ventana de page2. Observa la terminal. ¿Qué evento se registra ahora? ¿Qué datos ves?

- Experimento clave: cambia socket.broadcast.emit('getdata', page1); por 
socket.emit('getdata', page1); (quitando broadcast). Reinicia el servidor, abre ambas páginas. 
Mueve page1. ¿Se actualiza la visualización en page2? ¿Por qué sí o por qué no? 
(Pista: ¿A quién le envía el mensaje socket.emit?). Restaura el código a broadcast.emit.
:::

##### 8. Poner en marcha el Servidor

``` js
// Inicia el servidor y lo pone a escuchar en el puerto especificado
server.listen(port, () => {
    console.log(`Server is listening on http://localhost:${port}`);
});
```

:::note[🧩 Explicación] 
Esta es la línea final que efectivamente inicia el servidor.

server.listen(port, ...): le dice al servidor HTTP que empiece a escuchar conexiones en el port que definimos (3000).

La función () => { ... } es un callback que se ejecuta una vez que el servidor se ha 
iniciado correctamente y está listo para aceptar conexiones. Simplemente imprime un mensaje útil en la consola.
:::


:::caution[🧐🧪✍️ Experimenta]

- Detén el servidor.

- Cambia const port = 3000; a const port = 3001;.

- Inicia el servidor. ¿Qué mensaje ves en la consola? ¿En qué puerto dice que está escuchando?

- Intenta abrir http://localhost:3000/page1. ¿Funciona?

- Intenta abrir http://localhost:3001/page1. ¿Funciona?

- ¿Qué aprendiste sobre la variable port y la función listen? Restaura el puerto a 3000.
:::

:::caution[📤 Entrega]
Revisa tu bitácora. Deberías tener anotaciones y reflexiones para cada uno de los 
8 puntos y los experimentos asociados al servidor. Asegúrate de entender el flujo: 
recibir conexión -> escuchar eventos del cliente -> actualizar estado -> retransmitir a 
otros clientes.

