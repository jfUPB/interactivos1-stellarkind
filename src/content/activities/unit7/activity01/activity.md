#### Observa funcionando el caso de estudio

:::note[🎯 Enunciado]
Vamos a configurar y ejecutar el caso de estudio base. El objetivo es lograr que la interacción táctil en una página web abierta en tu **celular** controle en tiempo real un círculo en una página web abierta en el navegador de tu **computador**. Usaremos Node.js como intermediario y VS Code Dev Tunnels para hacer posible la conexión entre ambas aplicaciones.
:::

:::danger[⚠️ Advertencia]
En la unidad anterior usaste node.js para realizar el puente entre dos aplicaciones. En esta unidad 
usaremos el mismo concepto, pero ahora necesitas una herramienta adicional: **Dev Tunnels**. ¿Por qué?
Al terminar esta unidad verifica que tienes clara la respuesta a esta pregunta.
:::

:::tip[Recursos]
- Código fuente del caso de estudio (`server.js`, carpeta `public` con subcarpetas `desktop` y `mobile`).
- [Node.js](https://nodejs.org/en) instalado en tu computador.
- [Visual Studio Code](https://code.visualstudio.com/) instalado en tu computador.
- Tu teléfono celular con un navegador web moderno (Chrome, Safari, Firefox).
- Conexión a internet tanto en el computador como en el celular.
:::

👣 **Pasos**:

1.  **Prepara el proyecto:**
    *   Descarga o clona el código del caso de estudio en tu computador. El código está en 
    [este](https://github.com/juanferfranco/sfiSocketioDesktopMobile) repositorio.
    *   Abre una terminal en la carpeta raíz del proyecto.
    *   Ejecuta `npm install` para instalar las dependencias (`express`, `socket.io`). Haz esto solo la primera vez.
2.  **Inicia el servidor local:**
    *   Abre la carpeta del proyecto en VS Code.
    *   Abre una terminal integrada en VS Code (View > Terminal).
    *   En la terminal, ejecuta `npm start`.
    *   Deberías ver el mensaje: `Server is listening on http://localhost:3000`. ¡Pero aún no accedas a esa URL!
3.  **Expón el servidor con Dev Tunnels:**
    *   Selecciona PORTS. Click en Forward a Port (Dev Tunnels).
    *   En el número de puerto, selecciona `3000` (¿Por qué este?)
    *   En la columna Visibility, selecciona `Public`. Esto permitirá que el túnel sea accesible desde cualquier lugar.
    *   Copia la URL que aparece en la columna Forwarded Address. Esta URL es la que usarás para acceder al servidor 
        desde tu celular.
    *   Envía esta URL a tu celular. Se verá algo como `https://TU-TENDRAS-UNA-DIFERNTE.use2.devtunnels.ms/`.
4.  **Accede a las aplicaciones:**
    *   **En tu Computador:** abre un navegador web y ve a la URL: `http://localhost:3000/desktop/`. Deberías ver el canvas de p5.js con un círculo rojo.
    *   **En tu Celular:** abre un navegador web y ve a la URL que enviaste pero añadiendo /mobile/ al final. Algo así como esto:  `https://TU-TENDRAS-UNA-DIFERNTE.use2.devtunnels.ms//mobile/` (Asegúrate de añadir `/mobile/` al final). Deberías ver el canvas de p5.js con el texto "Touch to move the circle".
6.  **Prueba la interacción:**
    *   Toca y mueve el dedo sobre la pantalla de tu **celular**.
    *   Observa el navegador de tu **computador**. El círculo rojo debería moverse siguiendo tu dedo.
    *   Observa la terminal donde corre `server.js`. Deberías ver mensajes "New client connected", "Received message => ...", y "Client disconnected" cuando cierras las pestañas.
7.  **Cierra el Port**: una vez termines de hacer las pruebas **NO OLVIDES CERRAR** el puerto.
8.  **¿Si cerraste el puerto?**

:::note[🧐🧪✍️ Reporta en tu bitácora]
- ¿Qué URL de Dev Tunnels obtuviste? ¿Por qué crees que necesitamos usar esta URL en lugar de `http://localhost:3000` o 
la IP local de tu computador para que el celular se conecte?
- Describe brevemente qué hace `npm install` y `npm start`.
- ¿Qué mensajes observaste en la terminal del servidor al conectar el cliente de escritorio y el cliente móvil? ¿Eran diferentes los mensajes o identificadores?
- Describe el comportamiento observado: ¿Funcionó la interacción? ¿Hubo algún retraso (latencia)?
:::

:::caution[📤 Entrega]
Documenta en tu bitácora los pasos de configuración, la URL de Dev Tunnels obtenida, tus observaciones sobre los mensajes de la terminal, el funcionamiento de la interacción y tus respuestas a las preguntas de reflexión. Incluye capturas de pantalla si ayudan a ilustrar el proceso. ¿Cerraste el puerto? ¿Por qué es importante hacerlo?
:::
