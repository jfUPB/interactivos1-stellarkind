## Solución a la tercer actividad
### ¿Por qué crees que es útil usar módulos o librerías en lugar de escribir todo desde cero? ¿Qué ventajas aporta?
Cada ```require()``` trae un conjunto de herramientas que necesito. ```express``` parece ser la base para hacer servidores web más fácilmente, ```http``` es lo que usa Node.js para cosas web, ```socket.io``` es la clave para la comunicación instantánea que vi, y ```path``` es para manejar rutas de archivos de forma segura.  
Creo que la mayor ventaja es que me ahorro muchísimo trabajo. No tengo que reinventar la rueda; alguien más ya escribió el código para manejar cosas complejas como peticiones HTTP, rutas de archivos o comunicación en tiempo real.   
  
### ¿Qué ocurre al inicia el servidor y tratar de abrir ```http://localhost:3000/page1.html``` en el navegador?¿Por qué? ¿Qué mensaje ves en el navegador o en su consola de desarrollador (F12)?  
Detuve el servidor (```Ctrl+C```). Cambié la línea a ```app.use(express.static(path.join(__dirname, ‘archivos_cliente’)));```. Inicié el servidor de nuevo (```npm start```). Intenté abrir ```http://localhost:3000/page1.html``` en el navegador.  
Al hacer eso básicamente no funcionó, me sale un error de que no encuentra a ```page1.html```, creo que se debe a que al decirle a Express que buscara los archivos estáticos en una carpeta llamada ```archivos_cliente```, pero esa carpeta no existe, Express no pudo encontrar ```page1.html```.  

Volví a dejar la línea como estaba (```'views'```) y verifiqué que ```http://localhost:3000/page1.html``` funcionara de nuevo. Sí, funcionó correctamente, por lo que eniendo que la función ```express.static()``` es crucial para decirle al servidor desde qué carpeta debe entregar los archivos directamente cuando el navegador los pide, y que la ruta especificada debe ser correcta.  

### Al intentar acceder a ```http://localhost:3000/page1```. ¿Funciona?

### Al intentar acceder a ```http://localhost:3000/pagina_uno```. ¿Funciona?

### ¿Qué te dice esto sobre cómo el servidor asocia URLs con respuestas? Restaura el código.


