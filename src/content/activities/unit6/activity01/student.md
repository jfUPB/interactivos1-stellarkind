## Solución de la primer actividad
### Empezando con la configuración del entorno:
Abrimos el terminal, navegamos hasta la carpeta donde descargamos el repositorio, lo instalamos y ejecutamos el comando ```npm start```. Abrimos el navegador web, dos pestañas e ingresamos la ddirección en el servidor local de page1 y page2, tuve entoncs la oportunidad de comenzar a experimentar con las páginas, moviéndolas, cambiándolas de tamaño, y también duplicando páginas.

### ¿Qué ocurrió en la terminal cuando ejecutaste npm install? ¿Cuál crees que es su propósito? 
Al ejecutar el comando ```npm install``` la terminal mostró los paquetes y dependencias del repositorio, además creamos una carpeta de ```node modules``` dentro del royecto para descargar todas las librerías y herramientas que el proyecto va a necesitar como indicarn en las instrucciones (Node.js, Socket.IO), para dejar todo listo para que el servidor se pueda ejecutar.

### ¿Qué mensaje específico apareció en la terminal después de ejecutar npm start? ¿Qué indica este mensaje?
Luego de ejecutar ```npm start```, se lee:
```
> nodejs-test-1@1.0.0 start
> node server.js

Server is listening on http://localhost:3000
```

Que me indica que se inició correctamente, através de puerto de red #3000, pues justo después empiezo a recibir en el terminal la información de las ventanas de page1 y/o page2 que el programa utiliza para funcionar.

### Describe lo que ves inicialmente en page1 y page2 en tu navegador.
Al abrir page1 y page2 en pestañas diferentes del navegador, ambas parecen similares (son circulos rojos cuyo borde es negro, sobre un fondo blanco) pero con alguna diferencia geométrica sutil (cómo y donde se extiende la linea que llega a la mitad de ambos círculos color rojo).

### ¿Qué mensajes aparecieron en la terminal del servidor cuando abriste page1 y page2?
```
Server is listening on http://localhost:3000
A user connected - ID: 4fnEL1vPNT8rJNd_AAAB
Received win1update from ID: 4fnEL1vPNT8rJNd_AAAB Data: { x: 129, y: 185, width: 704, height: 328 }
A user connected - ID: T7w4PlHlyLUptLu-AAAD
Received win2update from ID: T7w4PlHlyLUptLu-AAAD Data: { x: 849, y: 212, width: 652, height: 346 }
```
Estos mensajes indicaban que se habían recibido nuevas conexiones. Esto me confirmó que el navegador estaba pidiendo las páginas al servidor, y que el servidor estaba reconociendo que dos clientes diferentes se habían conectado a él.

### Describe qué sucede en ambas páginas del navegador cuando mueves una de las ventanas. ¿Cambia algo visualmente? ¿Qué mensajes aparecen (si los hay) en la consola del navegador (usualmente accesible con F12 -> Pestaña Consola) y en la terminal del servidor?
Aquí fue donde vi la comunicación en tiempo real. Cuando moví la ventana del navegador de page1 por la pantalla, la linea que conecta ambos círculos se movió en tiempo real para reflejar la posición de ambas ventanas en la pantalla.
-  Abrí la consola en ambas pestañas. Mientras movía la pestaña, en la consola de la página que estaba moviendo, vi mensajes que indicaban que se estaban recibiendo los datos (ubicación y tamaño de la ventana) de la otra ventana.
    ```
    {x: 140, y: 93, width: 1068, height: 291}
    ```
- En la terminal donde corre el servidor, vi una ráfaga constante de mensajes mientras cualquiera de las ventanas.
    ```
    Received win2update from ID: -RExzMTQw5pU-UKWAAAF Data: { x: 337, y: 340, width: 666, height: 292 }
    Received win2update from ID: -RExzMTQw5pU-UKWAAAF Data: { x: 337, y: 339, width: 666, height: 292 }
    Received win1update from ID: CX1Z_zWYw_yZCng7AAAD Data: { x: 157, y: 156, width: 1068, height: 291 }
    Received win1update from ID: CX1Z_zWYw_yZCng7AAAD Data: { x: 157, y: 154, width: 1068, height: 291 }
    ```
    Estos mensajes indicaban que el servidor estaba recibiendo datos de un cliente y probablemente reenviando esos datos a todos los demás clientes conectados.

Esta primera interacción me mostró claramente el potencial de la comunicación en tiempo real a través de un servidor.  
