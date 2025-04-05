#### Retomando la metodología de diseño Input-Process-Output para el proyecto final

:::note[🎯 Enunciado]
Iniciamos la unidad final recordando y profundizando en un marco conceptual clave que vimos al inicio del curso: la metodología **Input-Process-Output (I-P-O)**. Ahora, la aplicaremos de forma central para guiar el diseño de tu proyecto integrador. Conectarás móvil, micro:bit y computador, usando el servidor Node.js como **puente de comunicación**, pero realizando todo el **procesamiento** y la generación del **output** en tu aplicación p5.js de escritorio.
:::

:::tip[Recursos]
*   Notas de la Unidad 1 sobre la introducción a I-P-O.
*   Texto de referencia sobre la metodología de Patrik Hübner (proporcionado como contexto).
*   Tus conocimientos acumulados de las unidades 1-7 (Serial, Socket.IO, p5.js, Node.js básico).
:::

👣 **Pasos**:

1.  **Refresca tu memoria sobre I-P-O:**
    *   Revisa tus notas de la Unidad 1 y el texto de Hübner.
    *   **Reflexiona:** ¿Cómo ha evolucionado tu comprensión de I-P-O ahora que has manejado múltiples inputs y outputs?
2.  **I-P-O con Procesamiento en el Cliente:**
    *   **Inputs:** identifica las fuentes de datos (Móvil vía Socket/Node, Micro:bit vía Serial, Computador local p5.js - teclado/ratón).
    *   **Puente (Node.js):** entiende su rol *limitado*, es decir, solo retransmitir mensajes entre el móvil y el cliente p5.js. No procesa ni almacena estado significativo.
    *   **Process (Cliente p5.js):** reconoce que *aquí* es donde combinarás los datos recibidos (del micro:bit directamente, del móvil vía Node.js, y locales), aplicarás tu lógica creativa y tomarás decisiones.
    *   **Output (Cliente p5.js):** la visualización que resulta de tu proceso local.
3.  **La intención guía la integración:** Discute cómo el modelo I-P-O te ayuda a diseñar la *lógica de integración* que correrá en tu cliente p5.js, asegurando que la combinación de inputs sirva a un propósito o narrativa.

:::note[🧐🧪✍️ Reporta en tu bitácora]
*   Describe cómo entiendes ahora el rol de cada componente (Móvil, Micro:bit, Servidor Node Puente, Cliente p5.js) dentro del esquema I-P-O modificado.
*   Explica por qué, aunque el servidor Node.js no procese, sigue siendo necesario en este esquema (para conectar el móvil).
*   Reafirma la importancia de la narrativa I-P-O para guiar la lógica de *procesamiento* que implementarás en p5.js. Anota ideas iniciales de narrativas que te interesen.
:::

:::caution[📤 Entrega]
Incluye en tu bitácora tus reflexiones actualizadas sobre I-P-O y tus ideas iniciales de narrativas.
:::

