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
