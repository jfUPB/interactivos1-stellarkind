#### Inputs y el puente Node.js

:::note[🎯 Enunciado]
Inicia la construcción implementando la captura de datos de entrada (micro:bit, móvil) y configurando el servidor Node.js como un simple **puente de mensajes**. Prepararás también la estructura básica del cliente p5.js para recibir estos datos.
:::

:::tip[Recursos]
*   Tu Plan Técnico (Actividad 4).
*   Código de referencia de unidades anteriores.
:::

👣 **Pasos**:

1.  **Configura el Entorno:**
    *   Estructura de carpetas para el proyecto. Puedes usar la misma estructura que en la unidad 7.
    *   Configura `server.js` básico (Express, HTTP, Socket.IO, static files).
2.  **Implementa código de inputs:**
    *   **micro:bit:** programa el envío de datos por serial según tu protocolo.
    *   **cliente móvil:** adapta/crea `public/mobile/sketch.js`. Código para conectar al servidor puente, 
    captura inputs y envío.
3.  **Implementa el servidor puente (Node.js):**
4.  **Prepara el Cliente p5.js (Receptor):**
    *   En `public/desktop/sketch.js`:
        *   Configura la conexión Socket.IO al servidor puente.
        *   Implementa la estructura básica para Web Serial (conectar, leer).
        *   Añade los listeners `socket.on('mobileInputForwarded', (data) => { ... });` y el código para parsear los datos seriales.
        *   Usa `console.log` dentro de estos listeners y en la lectura serial para verificar que los datos *crudos* llegan al cliente p5.js.
5.  **Prueba y Depura Flujo de Datos Crudos:**
    *   Verifica: ¿El Micro:bit envía datos? ¿El cliente p5.js los lee por Serial?
    *   Verifica: ¿El móvil envía datos al servidor puente? ¿El servidor los reenvía? ¿El cliente p5.js los recibe por Socket.IO?
    *   Enfócate SÓLO en que los datos lleguen correctamente al cliente p5.js. Aún no implementes el `Process`.

:::note[🧐🧪✍️ Reporta en tu bitácora]
*   Incluye fragmentos clave: programa micro:bit, cliente móvil (envío), servidor Puente (recepción/reenvío simple), cliente p5.js (conexión Serial, conexión Socket, listeners `on` con logs).
*   Documenta desafíos al configurar las conexiones o el reenvío.
*   Muestra evidencia (logs en la consola del navegador del cliente p5.js) de que los datos del micro:bit (Serial) y del móvil (Socket) llegan correctamente.
:::

:::caution[📤 Entrega]
Entrada de bitácora con fragmentos de código, desafíos y evidencia (logs) de la correcta recepción de datos crudos en el cliente p5.js.
:::
