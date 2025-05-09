#### Diseño técnico - arquitectura, flujos y protocolos

:::note[🎯 Enunciado]
Traduciremos tu concepto I-P-O a un plan técnico concreto, la arquitectura (componentes y relaciones). Diseñarás el flujo de datos, seleccionarás tecnologías y definirás protocolos, especificando el rol **exclusivamente de puente** del servidor Node.js y la ubicación del **proceso en el cliente p5.js** (la aplicación
del computador).
:::

:::tip[Recursos]
*   Tu Concepto I-P-O Final (Actividad 3).
*   Notas técnicas (Serial, Socket.IO, JSON, ASCII/Binario, Dev Tunnels).
*   Herramienta de diagramación.
:::

👣 **Pasos**:

1.  **Diagrama de arquitectura:**
    *   Dibuja los componentes de la aplicación: Móvil, Micro:bit, Computador (Cliente p5.js - **INCLUYE PROCESO Y OUTPUT**), Computador (Servidor Node.js - **SOLO PUENTE**).
    *   Incluye Dev Tunnels si es necesario para conectar Móvil <-> Servidor Puente.
    *   Dibuja flechas claras:
        *   Micro:bit -> Cliente p5.js (Serial).
        *   Móvil -> Servidor Puente (Socket.IO).
        *   Servidor Puente -> Cliente p5.js (Socket.IO - *reenviando datos del móvil*).
        *   Computador Local -> Cliente p5.js (Eventos p5.js - teclado/ratón).
2.  **Tecnologías de Comunicación:**
    *   Micro:bit -> Cliente p5.js: Web Serial API.
    *   Móvil -> Servidor Puente: Socket.IO.
    *   Servidor Puente -> Cliente p5.js: Socket.IO.
3.  **Protocolos de Datos Precisos:** Define formatos exactos:
    *   **Micro:bit -> Cliente p5.js (Serial):** Formato ASCII o binario.
    *   **Móvil -> Servidor Puente (Socket.IO):** el evento (ej: touch) y el formato JSON (ej: `{ "touchX": ..., "touchY": ... }`).
    *   **Servidor Puente -> Cliente p5.js (Socket.IO):** mismo JSON que recibió del móvil. *(El servidor no lo modifica)*.
4.  **Esquema Lógico del Servidor (¡Muy Simple!):**
    *   Describe en pseudocódigo/diagrama cómo `server.js`:
        *   Recibe evento del móvil.
        *   Inmediatamente, emite (`broadcast` o `io.emit`) el evento.
        *   (No almacena estado, no procesa datos).
5.  **Esquema Lógico del Cliente p5.js (Process + Output):**
    *   Describe en pseudocódigo/diagrama cómo `desktop/sketch.js`:
        *   Conecta y lee datos del Micro:bit (Web Serial), parsea.
        *   Conecta al Servidor Puente (Socket.IO).
        *   Escucha eventos del móvil, parsea JSON.
        *   Maneja inputs locales (teclado/ratón).
        *   **Implementa la lógica del `Process`:** almacena estado necesario, combina/interpreta datos de *todas* las fuentes (Serial, Socket, Local), aplica reglas/cálculos.
        *   Usa los resultados del proceso para actualizar variables de dibujo/interacción.
        *   Genera el `Output` visual/interactivo en `draw()`.

:::note[🧐🧪✍️ Reporta en tu bitácora]
*   Inserta el Diagrama de Arquitectura (Servidor Puente).
*   Lista Tecnologías de Comunicación.
*   Documenta protocolos precisos para cada flujo.
*   Incluye el esquema lógico **simple** del Servidor Puente.
*   Incluye el esquema lógico **detallado** del Cliente p5.js (incluyendo el Process).
:::

:::caution[📤 Entrega]
Incluye en tu bitácora el diagrama, tecnologías, protocolos, esquema MUY SIMPLE del servidor, esquema DETALLADO del cliente p5.js.
:::