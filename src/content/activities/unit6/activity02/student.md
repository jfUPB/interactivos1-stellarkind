## Solución a la segunda actividad
### 1. La rampa de acceso: ¿Qué pasaría si esa rampa se corta?
Normalmente me conecto con Wi-Fi en la universidad o con un cable de red en lacasa. Esas son mis "rampas" a la gran autopista de Internet. Si esa conexión se corta, es como si mi carro estuviera perfectamente bien y listo para ir, pero no pudiera salir del parqueadero. No puedo pedir páginas web, no puedo enviar mensajes, no puedo ir a ningún lado de forma conveniente. Mi computador se queda aislado de la red global.

### 2. Otros ejemplos de relaciones Cliente-Servidor (no digitales).  
**Cliente:** Yo.  
**Servidor:** El restaurante, representado por el camarero y la cocina.  
**Lo que se pide:** Un plato puntual del menú.  
**Lo que se entrega:** La comida preparada según mi pedido.  

-  Cliente soy yo pidiendo un producto, el Servidor es la tienda/vendedor que me muestra/ofrrece el producto.
-  Cliente soy yo como estudiante pidiendo un documento o información, el Servidor es la oficina del bloque 24 que me disponen esa información.
-  Cliente es alguien pidiendo un libro, el Servidor es la biblioteca que se lo presta.

### 3. Analizando mi URL favorita.  
Tomé la URL del canal de YouTube de Smosh (pues es el canal de la plataforma que más consumo): ```https://www.youtube.com/@smosh```.  
**Protocolo que usa:** ```https://```. Sé que ```https``` es la versión segura de ```http```.  
**Nombre de dominio:** ```www.youtube.com```.  
**Ruta específica:** ```/@smosh```.  

#### ¿Qué crees que pasa si solo escribes el nombre de dominio (ej. ```www.google.com```) sin una ruta específica? ¿Qué “página por defecto” crees que te envía el servidor?
Pensé en qué pasa si solo escribo el nombre de dominio (```www.google.com```). Cuando no especifico una ruta, creo que el navegador le pide al servidor la "raíz" o la página principal por defecto de ese dominio. Los servidores web suelen estar configurados para enviar un archivo específico (que suele ser llamado ```index.html``` según como hemos venido trabajando). Así que, cuando solo escribo ```www.google.com```, el servidor de Google me envía su página principal.

### 4. Comparando HTTP con protocolos seriales.  
**Similitudes:**  
- Ambos son protocolos, conjuntos de reglas para que dos partes se entiendan.  
- Ambos involucran un emisor y un receptor que deben "hablar el mismo idioma".  
- Ambos se encargan de empaquetar datos para su transmisión.
 
**Diferencias:**
- HTTP opera a una escala mucho mayor, sobre una red global, mientras que mis protocolos seriales eran para comunicación directa y punto a punto entre dos dispositivos conectados por un cable.  
- HTTP es un protocolo de petición/respuesta. El cliente pide, el servidor responde, y la conexión suele cerrarse después de cada intercambio. Los protocolos seriales que usamos eran más de flujo continuo de datos o paquetes de datos.  

**¿Por qué HTTP necesita ser más complejo?**
- El internet es una red muy grande y que no es muy confiable siempre, entonces HTTP necesita manejar el enrutamiento de peticiones a servidores específicos en cualquier parte del mundo.
- a web no es solo datos de sensores; hay páginas HTML, imágenes, videos, archivos, APIs, etc. HTTP necesita una forma de especificar qué tipo de recurso se pide y qué tipo de contenido se está enviando en la respuesta.
- En una red tan grande, las cosas pueden salir mal (servidor no encontrado, recurso movido, error interno del servidor). HTTP tiene códigos para informarle al cliente exactamente qué pasó con su petición(el 404 es el más común que yo he visto).

### 5. HTML, CSS y JavaScript en un formulario de login.
Inspeccionando diferentes paginas con formulario de login llegué a la siguiente clasificación:
- **HTML:** Son los elementos estructurales por ejemplo la etiqueta ```<form>```, los campos de entrada para el nombre de usuario y la contraseña (```<input type="text">``` o ```<input type="password">```), las etiquetas ```<label>``` que dicen "Usuario o Correo" y "Contraseña", y el botón para enviar (```<button type="submit">```).
- **CSS:** Al ser la apariencia, incluye el diseño de la caja que contiene el formulario, los colores del fondo y del texto, el tipo y tamaño de letra, cómo se alinean los campos, el color y el diseño del botón, y cómo cambia el botón cuando paso el ratón por encima.
- **JavaScript:** Es la interactividad y la validación, entonces si intento enviar el formulario con campos vacíos, el código JavaScript puede detectar eso y mostrar un mensaje de error sin recargar la página. También puede validar si el formato de un email es correcto.

### 6. Modelo de ejecución: draw() vs. basado en eventos.
Comparando el bucle ```draw()``` de p5.js con el modelo basado en eventos de mucha programación web:
En p5.js, ```setup()``` hace la configuración inicial y luego ```draw()``` se repite constantemente, redibujando el lienzo una y otra vez. Yo le digo al programa qué hacer en cada fotograma. Mientras que el modelo basado en eventos, el código "espera" pasivamente a que suceda algo, cuando ese "evento" ocurre, se ejecuta una función específica que yo definí para reaccionar a ese evento. Entonces el código no se ejecuta en un bucle constante por defecto.

#### Ventajas del modelo basado en eventos para una interfaz de usuario web: 
La mayor ventaja es la eficiencia. La mayoría del tiempo, un usuario no está haciendo nada en una página web; está leyendo, pensando. Si la página entera se estuviera redibujando 60 veces por segundo con un bucle draw(), sería un desperdicio enorme de recursos del computador, la página sería lenta y el navegador estaría trabajando sin parar. 

#### ¿Sería eficiente tener un bucle ```draw()``` redibujando toda la página 60 veces por segundo si nada ha cambiado?
No, pues como dije antes sería mucho más ineficiente. Consumiría una gran cantidad de CPU y memoria sin necesidad, haría que la interfaz se sintiera lenta y podría incluso hacer que el navegador no respondiera correctamente. El bucle draw() funciona bien para el lienzo de p5.js porque ese es un área específica diseñada para ser redibujada constantemente para animaciones o gráficos interactivos, pero para la mayoría de páginas web no es necesario y entorpesería el rendimiento de la misma.

### 7. ¿Por qué podría ser útil usar JavaScript tanto en el cliente como en el servidor?
Creo que la principal ventaja de usar JavaScript tanto en el cliente (el código que se ejecuta en el navegador, como en p5.js y los scripts de mis páginas) como en el servidor (con Node.js) es para los desarrolladores. Si ya sé JavaScript para la parte visual e interactiva de una página web, poder usar el mismo lenguaje para escribir la lógica del servidor (cómo manejar peticiones, interactuar con bases de datos, gestionar conexiones) me ahorra el tener que aprender otro lenguaje completamente diferente para el backend.

###  Pausa activa final: Resumiendo Socket.IO y sus aplicaciones.
- La comunicación HTTP tradicional es un modelo de petición-respuesta único. El cliente pide algo, el servidor le responde, y la conexión para esa petición específica generalmente se cierra. Es como enviar una carta o un correo electrónico: pido algo, me responden, y la comunicación se pausa hasta que yo (o el servidor, en algunos casos especiales) inicio otra petición.
- La comunicación usando ```WebSockets```/```Socket.IO``` establece una conexión persistente y bidireccional entre el cliente y el servidor una vez que se negocia inicialmente. Una vez abierta, tanto el cliente como el servidor pueden enviarse mensajes entre sí en tiempo real, en cualquier momento, sin necesidad de que el cliente inicie cada comunicación con una nueva petición HTTP. Es como tener una llamada telefónica o un chat en vivo, por ello esta comunicación en tiempo real se utiliza en aplicaciones de chat, juegos en linea multijugador, herramientas como google docs o canva, para recibir notificaciones de aplicaciones que se están ejecutando en segundo plano o el caso de estudio con el que empezamos esta unidad donde se comparte la posición de las ventanas entre ambos clientes en tiempo real.
