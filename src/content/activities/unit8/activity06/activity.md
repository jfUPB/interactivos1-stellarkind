#### Proceso y Output en p5.js

:::note[🎯 Enunciado]
Completa tu proyecto implementando la lógica de **procesamiento (`Process`)** y la generación del **output (`Output`)** directamente en tu sketch de **cliente p5.js**. Combinarás los datos recibidos (Serial, Socket, Local) y crearás la visualización/interacción final.
:::

:::tip[Recursos]
*   Tu Plan Técnico (Actividad 4) y Concepto I-P-O (Actividad 3).
*   Código implementado en Actividad 5 (cliente p5.js ya recibe datos crudos).
:::

👣 **Pasos**:

1.  **Implementa el Process en Cliente p5.js:**
    *   Maneja también inputs locales (teclado/ratón) si forman parte de tu concepto, actualizando variables de estado.
    *   **Crea la lógica principal del `Process`:** escribe funciones o código (puede ser dentro de `draw()` o en funciones separadas llamadas desde `draw()`) que lean las variables de estado (con los últimos datos de todas las fuentes), las combinen, apliquen tus reglas/cálculos definidos en el concepto I-P-O, y determinen los parámetros para el output (ej: tamaño, color, posición, sonido).
2.  **Implementa el Output en Cliente p5.js:**
    *   En la función `draw()`: Usa los parámetros calculados por tu lógica de `Process` para dibujar en el canvas, modificar elementos visuales, controlar sonido, etc.
    *   Asegúrate de que el output refleje visual o interactivamente la combinación de los inputs según tu narrativa.
3.  **Pruebas de Integración (End-to-End con proceso local):**
    *   Ejecuta todo: servidor puente (simple relay), móvil, conecta micro:bit, escritorio (p5.js).
    *   Interactúa con *todos* los inputs.
    *   Observa si el Output en p5.js reacciona como esperabas, reflejando la lógica de combinación y procesamiento que implementaste *ahí mismo*.
    *   Depura la lógica del `Process` *dentro* de p5.js usando `console.log` para ver valores intermedios y el resultado del procesamiento antes de dibujar.
4.  **Refinamiento:** ajusta la lógica del proceso, la estética del output, la sensibilidad a los inputs, etc., para lograr el efecto deseado y comunicar tu concepto eficazmente.

:::note[🧐🧪✍️ Reporta en tu bitácora]
*   Incluye fragmentos clave del código del **cliente p5.js**:
    *   Cómo almacenas/actualizas estado desde Serial y Socket.
    *   La lógica principal del `Process` (funciones o parte relevante de `draw()`).
    *   Cómo usas los resultados del `Process` para generar el `Output` en `draw()`.
*   Describe el comportamiento final. ¿Lograste implementar tu lógica de integración en p5.js? ¿El output refleja la combinación de inputs?
*   Documenta desafíos específicos al implementar el `Process` en p5.js y al depurar la interacción completa.
*   **Incluye el vídeo final mostrando el sistema completo funcionando.** NOTA: esta vez si el video NO EL ENLACE.
Este video servirá de inspiración para tus compañeros del próximo semestre y como parte de tu portafolio 
profesional.
:::

:::caution[📤 Entrega]
Entrada de bitácora con fragmentos de código del cliente p5.js (proceso y output), descripción del comportamiento, desafíos y la evidencia visual final.
:::
