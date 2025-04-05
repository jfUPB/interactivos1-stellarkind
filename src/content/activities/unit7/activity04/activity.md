#### Análisis del cliente móvil (`mobile/sketch.js`) y de escritorio (`desktop/sketch.js`)

:::note[🎯 Enunciado]
Ahora analizaremos el código que corre en los navegadores: el cliente móvil que captura el toque (`mobile/sketch.js`) y el cliente de escritorio que recibe la información y dibuja (`desktop/sketch.js`). Veremos cómo usan Socket.IO para comunicarse con el servidor.
:::

:::tip[Recursos]
*   Archivos `public/mobile/index.html`, `public/mobile/sketch.js`, `public/desktop/index.html`, `public/desktop/sketch.js`.
*   [Documentación de p5.js (especialmente `touchMoved`, `mouseX`, `mouseY`)](https://p5js.org/reference/)
*   [Documentación de Socket.IO (Client API)](https://socket.io/docs/v4/client-api/)
:::

👣 **Pasos**: (Análisis del código)

##### 1. Cliente móvil (`mobile/sketch.js`) - El Emisor

```javascript
// mobile/sketch.js (partes clave)
let socket;
let lastTouchX = null;
let lastTouchY = null;
const threshold = 5; // Umbral para evitar enviar demasiados mensajes

function setup() {
    // ... createCanvas, background ...
    socket = io();

    socket.on('connect', () => console.log('Connected to server'));
    // ... otros listeners de socket ('message', 'disconnect', 'connect_error') ...
}

function touchMoved() { // Función especial de p5.js para eventos táctiles
    if (socket && socket.connected) {
        // Calcula si el movimiento supera el umbral
        let dx = abs(mouseX - lastTouchX);
        let dy = abs(mouseY - lastTouchY);

        if (dx > threshold || dy > threshold || lastTouchX === null) { // Enviar si supera umbral o es el primer toque
            let touchData = {
                type: 'touch', // Tipo de mensaje (podríamos tener otros)
                x: mouseX,     // Coordenada X del toque (relativa al canvas móvil)
                y: mouseY      // Coordenada Y del toque
            };
            // Envía el objeto como una cadena JSON al servidor
            socket.emit('message', JSON.stringify(touchData));

            // Actualiza la última posición registrada
            lastTouchX = mouseX;
            lastTouchY = mouseY;
        }
    }
    return false; // Evita comportamiento default del navegador en móviles
}
```

:::note[🧩 Explicación (Móvil)]

setup(): Se conecta al servidor Socket.IO usando la URL del Dev Tunnel. Se configuran listeners básicos para saber si la conexión fue exitosa, si llega algún mensaje (aunque este cliente no espera recibir datos relevantes), o si hay errores/desconexión.

touchMoved(): Esta función de p5.js se llama automáticamente cada vez que el usuario mueve el dedo sobre la pantalla táctil (dentro del canvas).

Umbral (threshold): Se introduce una optimización. Para no saturar la red enviando un mensaje por cada mínimo pixel de movimiento, solo se envía si el dedo se ha movido más de threshold píxeles desde la última vez que se envió un mensaje. También se envía siempre en el primer toque (lastTouchX === null).

touchData: Se crea un objeto JavaScript con la información relevante: un type (útil si quisiéramos enviar distintos tipos de datos) y las coordenadas x e y del toque (mouseX, mouseY en p5.js registran la posición del último toque/ratón).

JSON.stringify(touchData): El objeto touchData se convierte a una cadena de texto en formato JSON. Es una práctica común y robusta para enviar datos estructurados a través de la red.

socket.emit('message', ...): Se envía el evento 'message' al servidor, llevando la cadena JSON como dato.

return false;: Previene comportamientos por defecto del navegador en eventos táctiles (como hacer scroll o zoom), que podrían interferir.
:::

:::caution[🧐🧪✍️ Reflexiona (Móvil)]

¿Por qué es útil enviar los datos como un objeto JSON ({type: 'touch', x: ..., y: ...}) en lugar de simplemente enviar, por ejemplo, una cadena como "mouseX,mouseY"?

¿Qué pasaría si quitaras la comprobación del threshold? ¿Cómo afectaría al rendimiento o la fluidez de la interacción?

¿Cómo adaptarías este código si quisieras que también respondiera al clic del ratón en un computador (para pruebas)? (Pista: p5.js tiene mouseMoved() y mousePressed()).
:::

##### 2. Cliente de Escritorio (`desktop/sketch.js`) - El Receptor

```javascript
// desktop/sketch.js (partes clave)
let socket;
let circleX = 200; // Posición inicial X
let circleY = 200; // Posición inicial Y

function setup() {
    // ... createCanvas, background ...
    socket = io();

    socket.on('connect', () => console.log('Connected to server'));

    // Listener clave: se ejecuta cuando llega un mensaje del servidor
    socket.on('message', (data) => {
        console.log(`Received message: ${data}`);
        try {
            // Intenta convertir la cadena JSON de vuelta a un objeto
            let parsedData = JSON.parse(data);
            // Verifica si es un mensaje de tipo 'touch'
            if (parsedData && parsedData.type === 'touch') {
                // Actualiza las coordenadas del círculo con los datos recibidos
                // ¡Ojo! Las coordenadas vienen del canvas móvil.
                // Aquí simplemente las usamos, pero en un caso real podríamos necesitar mapearlas
                // si los canvas tuvieran tamaños diferentes.
                circleX = parsedData.x;
                circleY = parsedData.y;
            }
        } catch (e) {
            console.error("Error parsing received JSON:", e);
        }
    });

    // ... otros listeners ('disconnect', 'connect_error') ...
}

function draw() {
    background(220);
    fill(255, 0, 0);
    ellipse(circleX, circleY, 50, 50); // Dibuja el círculo en la posición actualizada
}
```

:::note[🧩 Explicación (Escritorio)]

setup(): similar al móvil, se conecta al mismo servidor Socket.IO usando la URL del túnel.

socket.on('message', (data) => { ... });: este es el listener crucial. Se activa cada vez que el servidor (re)envía un evento 'message'. La variable data contiene la información enviada por el servidor (la cadena JSON que originalmente vino del móvil).

JSON.parse(data): la cadena JSON recibida (data) se convierte de nuevo en un objeto JavaScript (parsedData). Es importante usar un try...catch porque si data no fuera un JSON válido, JSON.parse daría un error y detendría el script.

if (parsedData && parsedData.type === 'touch'): se verifica que el objeto exista y que tenga la propiedad type con el valor 'touch'. Esto asegura que estamos procesando el tipo correcto de mensaje.

circleX = parsedData.x; circleY = parsedData.y;: se actualizan las variables globales circleX y circleY con las coordenadas recibidas del móvil.

draw(): la función draw de p5.js se ejecuta continuamente. Simplemente dibuja el fondo y luego el círculo rojo usando las últimas coordenadas circleX y circleY disponibles. Como estas variables se actualizan cuando llega un mensaje, el círculo parece moverse en tiempo real.
:::

:::caution[🧐🧪✍️ Reflexiona (Escritorio)]

¿Por qué es importante usar JSON.parse() dentro de un bloque try...catch?

Si los canvas del móvil y del escritorio tuvieran tamaños diferentes (ej: móvil 300x300, escritorio 600x600), ¿cómo modificarías el código del escritorio para que la posición del círculo rojo refleje proporcionalmente la posición del toque en el móvil? (Pista: usa la función map() de p5.js).

¿Qué tendrías que cambiar en el código del escritorio si el servidor, en lugar de retransmitir el evento como 'message', lo enviara como 'updateDesktop'?
:::

:::note[🧐🧪✍️ Reporta en tu bitácora]

Describe el propósito principal de mobile/sketch.js y desktop/sketch.js.

Explica la función touchMoved en el móvil, incluyendo el uso del threshold y JSON.stringify.

Explica cómo el cliente de escritorio recibe (socket.on) y procesa (JSON.parse, chequeo de type) los datos para actualizar la posición del círculo.

Responde a las preguntas de reflexión de las secciones del móvil y del escritorio.
:::

:::caution[📤 Entrega]
Incluye en tu bitácora las descripciones y explicaciones solicitadas sobre ambos scripts cliente, así como tus respuestas a las preguntas de reflexión.
:::
