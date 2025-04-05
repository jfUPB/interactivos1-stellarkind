#### Conceptos clave: Dev Tunnels, JSON y eventos táctiles

:::note[🎯 Enunciado]
Antes de analizar el código línea por línea, vamos a clarificar los conceptos *nuevos* o *críticos* específicos de esta unidad que permiten la comunicación entre tu celular y tu computador a través de Internet.
:::

:::tip[Recursos]
- Documentación de [VS Code Dev Tunnels](https://code.visualstudio.com/docs/debugtest/port-forwarding) (para profundizar).
- Documentación sobre [JSON](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/JSON) (MDN).
- Referencia de p5.js sobre eventos táctiles: [`touchMoved()`](https://p5js.org/reference/p5/touchMoved/).
- Tu código del caso de estudio.
:::

👣 **Pasos**: (Explicación conceptual)

1.  **El problema de la conexión directa:**
    *   Cuando ejecutas `npm start`, el servidor escucha en `localhost:3000`. `localhost` (o `127.0.0.1`) es una dirección especial que *siempre* se refiere a tu propia máquina.
    *   Si intentaras acceder a `http://localhost:3000` desde tu celular, este buscaría un servidor *en el propio celular*, no en tu computador.
    *   Podrías usar la IP local de tu computador (ej. `192.168.1.X`), pero esto solo funciona si ambos dispositivos están en la *misma red Wi-Fi* y no hay firewalls bloqueando. No funcionaría si tu celular usa datos móviles o está en otra red.
    *   **Necesitamos una dirección pública y accesible desde cualquier lugar.**

2.  **La solución: VS Code Dev Tunnels (Port Forwarding):**
    *   Dev Tunnels actúa como un **intermediario seguro**. Crea un túnel desde una URL pública en Internet (la que obtuviste, como `https://TU-TENDRAS-UNA-DIFERNTE.use2.devtunnels.ms/`) hasta el puerto `3000` de tu `localhost`.
    *   Cuando tu celular (o cualquier cliente en Internet) se conecta a la URL pública de Dev Tunnels, el servicio de Dev Tunnels reenvía esa conexión de forma segura a través del túnel hasta tu servidor Node.js local.
    *   Del mismo modo, las respuestas de tu servidor local viajan de vuelta por el túnel hasta el servicio Dev Tunnels, que las entrega al cliente (celular/escritorio).
    *   **Analogía:** Es como tener un número de teléfono público (la URL de Dev Tunnels) que redirige las llamadas a tu teléfono privado en casa (`localhost:3000`), sin exponer directamente tu número privado.

3.  **Enviando datos estructurados: JSON:**
    *   Queremos enviar más que un simple número o texto. Necesitamos enviar la posición táctil, que tiene coordenadas `x` e `y`. Podríamos enviarlos como "120,250", pero es mejor estructurarlo. Recuerdas los protocolos ASCII y binario de las unidades anteriores? 
    *   Creamos un objeto JavaScript en el cliente móvil: `let touchData = { type: 'touch', x: mouseX, y: mouseY };`. Esto es claro y extensible (podríamos añadir más datos en el futuro).
    *   Sin embargo, Socket.IO (y muchas comunicaciones en red) envían datos como **strings** (cadenas de texto). No podemos enviar un objeto JavaScript directamente.
    *   **`JSON.stringify(touchData)`:** Convierte el objeto JavaScript en una cadena de texto con formato JSON. Ejemplo: `'{"type":"touch","x":120,"y":250}'`. Esta cadena SÍ puede enviarse por la red.
    *   **`JSON.parse(data)`:** En el cliente receptor (escritorio), se recibe la cadena JSON. Esta función la convierte de nuevo en un objeto JavaScript utilizable: `{ type: 'touch', x: 120, y: 250 }`.

4.  **Capturando la entrada: eventos táctiles en p5.js (`mobile/sketch.js`):**
    *   p5.js ofrece funciones específicas que se ejecutan automáticamente cuando ocurren eventos táctiles en un dispositivo móvil (o simulación en escritorio).
    *   **`touchMoved()`:** es la función clave aquí. Se llama *continuamente* mientras el usuario mantiene un dedo presionado y lo mueve sobre el canvas. Dentro de esta función, `mouseX` y `mouseY` contienen las coordenadas actuales del toque.
    *   **Optimización (`threshold`):** el código no envía un mensaje en *cada* pequeño movimiento detectado por `touchMoved()`. Comprueba si el movimiento desde la última vez que se envió (`lastTouchX`, `lastTouchY`) supera un umbral (`threshold`). Esto evita inundar la red con mensajes si el dedo tiembla o se mueve mínimamente, enviando solo cambios significativos.
    *   Otras funciones útiles (no usadas en este caso base, pero relevantes):
        *   `touchStarted()`: se llama una vez cuando el usuario toca la pantalla por primera vez.
        *   `touchEnded()`: se llama una vez cuando el usuario levanta el dedo de la pantalla.

:::note[🧐🧪✍️ Reporta en tu bitácora]
- Explica con tus propias palabras: ¿Por qué es necesario Dev Tunnels en este escenario y cómo funciona conceptualmente?
- ¿Por qué usamos `JSON.stringify()` en el emisor (móvil) y `JSON.parse()` en el receptor (escritorio)? ¿Qué problema resuelve JSON aquí?
- Describe la función de `touchMoved()` y por qué se usa la variable `threshold` en el cliente móvil.
- ¿Qué otros eventos táctiles existen en p5.js y para qué tipo de interacciones podrían ser útiles (ej. un botón virtual, detectar un tap)?
- Compara brevemente Dev Tunnels con simplemente usar la IP local. ¿Cuáles son las ventajas y desventajas de cada uno?
:::

:::caution[📤 Entrega]
Documenta en tu bitácora tus explicaciones y respuestas a las preguntas sobre estos conceptos clave.
:::
