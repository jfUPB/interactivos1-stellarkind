## Solución a la tercer actividad
### ¿Por qué crees que es útil usar módulos o librerías en lugar de escribir todo desde cero? ¿Qué ventajas aporta?
Cada ```require()``` trae un conjunto de herramientas que necesito. ```express``` parece ser la base para hacer servidores web más fácilmente, ```http``` es lo que usa Node.js para cosas web, ```socket.io``` es la clave para la comunicación instantánea que vi, y ```path``` es para manejar rutas de archivos de forma segura.  
Creo que la mayor ventaja es que me ahorro muchísimo trabajo. No tengo que reinventar la rueda; alguien más ya escribió el código para manejar cosas complejas como peticiones HTTP, rutas de archivos o comunicación en tiempo real.   
  
### ¿Qué ocurre al inicia el servidor y tratar de abrir ```http://localhost:3000/page1.html``` en el navegador?¿Por qué? ¿Qué mensaje ves en el navegador o en su consola de desarrollador (F12)?  
Detuve el servidor (```Ctrl+C```). Cambié la línea a ```app.use(express.static(path.join(__dirname, ‘archivos_cliente’)));```. Inicié el servidor de nuevo (```npm start```). Intenté abrir ```http://localhost:3000/page1.html``` en el navegador.  
Al hacer eso básicamente no funcionó, me sale un error de que no encuentra a ```page1.html```, creo que se debe a que al decirle a Express que buscara los archivos estáticos en una carpeta llamada ```archivos_cliente```, pero esa carpeta no existe, Express no pudo encontrar ```page1.html```.  

Volví a dejar la línea como estaba (```'views'```) y verifiqué que ```http://localhost:3000/page1.html``` funcionara de nuevo. Sí, funcionó correctamente, por lo que eniendo que la función ```express.static()``` es crucial para decirle al servidor desde qué carpeta debe entregar los archivos directamente cuando el navegador los pide, y que la ruta especificada debe ser correcta.  

### Al intentar acceder a ```http://localhost:3000/page1```. ¿Funciona?
Detuve el servidor. Cambié la primera ruta de ```/page1``` a ```/pagina_uno```. Inicié el servidor. Intenté acceder a ```http://localhost:3000/page1```. No funcionó. Vi un error 404 Not Found en el navegador.

### Al intentar acceder a ```http://localhost:3000/pagina_uno```. ¿Funciona?
Luego intenté acceder a ```http://localhost:3000/pagina_uno```. Esta sí funcionó.

### ¿Qué te dice esto sobre cómo el servidor asocia URLs con respuestas? Restaura el código.
Esto me mostró muy claramente que el servidor solo responderá a las rutas que yo defina explícitamente con ```app.get()```. Si el navegador pide una ruta que no está definida, el servidor no sabe qué hacer o no encuentra esa ruta definida.

### Abre http://localhost:3000/page1 en una pestaña. Observa la terminal del servidor. ¿Qué mensaje ves?
En la terminal del servidor, vi el mensaje ```A user connected - ID: PHT2cqq6VIU1I2isAAAB```

### Abre http://localhost:3000/page2 en otra pestaña. Observa la terminal del servidor. ¿Qué mensaje ves? ¿El ID es diferente?
Vi otro mensaje ```A user connected - ID: JEgvOpyiNRqP6CE3AAAD```

### Cierra la pestaña de page1. Observa la terminal. ¿Qué mensaje ves? ¿Coincide el ID con el que anotaste?
 ```User disconnected - ID: PHT2cqq6VIU1I2isAAAB``` es el mensaje en la terminal, efectivmente coincide con el primer usuario, el de page1.
 
###  Cierra la pestaña de page2. Observa la terminal.
Vi el mensaje ```User disconnected - ID: JEgvOpyiNRqP6CE3AAAD```. También coincidía.

### Mueve la ventana de page1. Observa la terminal del servidor. ¿Qué evento se registra (win1update o win2update)? ¿Qué datos (Data:) ves?
Me aseguré de que los console.log estuvieran en mi server.js. Inicié el servidor y abrí page1 y page2.
Moví la ventana de page1. En la terminal del servidor, vi mensajes como ```Received win1update from ID: PHT2cqq6VIU1I2isAAAB Data: { x: ..., y: ..., width: ..., height: ... }```. Donde se registró el evento win1update con los datos de posición y tamaño de esa ventana.

### Mueve la ventana de page2. Observa la terminal. ¿Qué evento se registra ahora? ¿Qué datos ves?
Moví la ventana de page2. Vi mensajes como Received win2update from ```ID: JEgvOpyiNRqP6CE3AAAD Data: { x: ..., y: ..., width: ..., height: ... }```. Se registró el evento win2update con los datos de la ventana 2.

### Ahora, el experimento clave: 
Detuve el servidor. Cambié ```socket.broadcast.emit('getdata', page1);``` por ```socket.emit('getdata', page1);``` en el manejador de win1update (y no lo hice en el de win2update para ver la diferencia). Reinicié el servidor, abrí ambas páginas. Moviendo page1. page2 no se ve ¿por qué? ```socket.emit()``` envía un mensaje SOLO al cliente asociado con ese objeto socket (es decir, al cliente que envió el mensaje win1update en este caso). ```socket.broadcast.emit()``` envía el mensaje a todos los demás clientes conectados MENOS al que lo envió. Como quité broadcast en el manejador de win1update, el servidor recibía el dato de page1, pero luego intentaba enviárselo de vuelta a page1 misma (socket.emit), no a page2. Por eso page2 no se enteraba del movimiento.

### Detén el servidor. Cambia ```const port = 3000;``` a ```const port = 3001;```. Inicia el servidor. ¿Qué mensaje ves en la consola? ¿En qué puerto dice que está escuchando?
En la consola, el mensaje de inicio cambió a ```Server is listening on http://localhost:3001```. Al intentar abrir ```http://localhost:3000/page1``` no se pudo, el navegador no pudo conectarse porque no había nada escuchando en el puerto 3000. Entonces intenté abrir ```http://localhost:3001/page1``` y el navegador se conectó al servidor en el puerto 3001 y me entregó la página. Asumo entoncs que la variable ```port``` define el número del puerto en el que mi servidor va a operar, y ```server.listen()``` es la función que lo pone a "escuchar" en ese puerto específico.
