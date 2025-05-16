## Solución a la septima actividad
### Describe qué aprendiste en esta unidad.
En esta unidad, aprendí mucho sobre cómo conectar el mundo físico (con el micro:bit y sus sensores/botones) con el mundo digital creativo (gracias a  p5.js). El concepto principal que se me quedó muy claro es la comunicación serial. Aprendí que es una forma de enviar datos bit a bit entre dos dispositivos, y que para que funcione bien, ambos deben seguir un protocolo definido: cómo se estructuran los datos (en mi caso, valores separados por comas), qué carácter marca el fin de un mensaje (el salto de línea \n), y la velocidad a la que se comunican (la tasa de baudios). Además, consolidé mi entendimiento de las máquinas de estado en p5.js. Aprendí que son súper útiles para manejar diferentes comportamientos de la aplicación según su estado (esperando conexión vs. ejecutándose y usando datos), y cómo gestionar las transiciones entre esos estados basadas en eventos (como que la conexión serial se establezca o se pierda).

### ¿Qué fue lo más difícil de esta unidad? ¿Por qué? ¿Y lo más fácil? ¿por qué?
Para mí, lo más difícil fue la parte de depurar la comunicación serial, especialmente en p5.js. A veces, recibía ese mensaje de "No se están recibiendo 4 datos del micro:bit", y aunque entendí por qué ocurría conceptualmente (datos mal formados o incompletos), fue un poco frustrante al principio no tener un control total sobre por qué sucedían esos errores de transmisión ocasionales. En cambio me resultó mucho más sencillo entender la diferencia entre ```is_pressed()``` y ```was_pressed()``` una vez que los probé., así también integrar la máquina de estados fue más fácil ahora, ya tenía una base de la unidad anterior.

### ¿Cuánto tiempo dedicaste a esta unidad? ¿Fue suficiente? Dada la cantidad de créditos académicos del curso, semanalmente debes dedicar tres sesiones de trabajo, dos en el aula y una más por fuera de ella. ¿Pudiste dedicar las seis sesiones? ¿Por qué?
Dediqué un tiempo considerable a esta unidad, creo que sí se acercó a las seis sesiones de trabajo o quizás un poco más, sobre todo porque la parte de la integración y depuración de la comunicación serial me tomó tiempo extra fuera de las sesiones guiadas. El material es denso y la integración de hardware y software siempre añade una capa de complejidad que requiere probar y ajustar. Sentí que el tiempo que le dediqué fue necesario para entender realmente los conceptos y lograr que las aplicaciones finales funcionaran.

### ¿Qué podrías mejorar en tu proceso de aprendizaje en esta unidad si la tuvieras que hacer de nuevo?
Si tuviera que hacer esta unidad de nuevo, creo que dedicaría más tiempo a visualizar el flujo de la máquina de estados antes de empezar a escribir códgo.

### Piensa en tu perfil profesional y describe cómo lo que aprendiste en esta unidad te puede ser útil en el futuro en tu vida profesional.
Lo que aprendí en esta unidad es increíblemente útil para mi futuro profesional, especialmente si me interesa el área de la interacción física, Internet de las Cosas (IoT) o las instalaciones interactivas. Saber cómo un dispositivo físico como un microcontrolador puede enviar datos a una aplicación en un pc, y cómo esa aplicación puede recibir e interpretar esos datos para hacer algo (ya sea visualización, control, o arte), abre muchas puertas.

### ¿Qué te gustaría aprender en la siguiente unidad?
En la siguiente unidad, me gustaría explorar cómo enviar información desde p5.js hacia el micro:bit (comunicación bidireccional), ya que en el pasado lo he intentado pero ha sido mucho más complejo luego del tema de máquina de estados.

### ¿Cómo estuvo tu estado de ánimo durante esta unidad? ¿Por qué?
Mi estado de ánimo durante esta unidad fue mayormente positivo. Al principio sentí curiosidad por cómo iba a funcionar la conexión. Hubo momentos de desafío y quizás un poco de frustración cuando el código serial no funcionaba a la primera o aparecían errores inesperados pero tuve que aprender a estar bien con eso o buscar ayuda.

### La motivación se define como aquellos procesos cognitivos y afectivos que hacen que puedas iniciar, mantenerte y poner el esfuerzo necesario para lograr una meta. ¿Cómo estuvo tu motivación durante esta unidad? ¿Por qué?
Mi motivación estuvo bastante alta durante esta unidad. La meta de conectar hardware y software y ver una aplicación interactiva tangible me impulsó mucho. No era solo código abstracto; era algo que podía sostener en mis manos (el micro:bit) y ver cómo afectaba directamente algo en la pantalla; lo que va muy de la mano con mi inteligencia mecánica.

### ¿Qué tan satisfecha estás con tu desempeño en esta unidad? ¿Por qué?
Estoy muy satisfecha con mi desempeño en esta unidad, del uno al cinco yo creo que un cuatro. Siento que logré entender los conceptos clave de la comunicación serial y las máquinas de estado, y fui capaz de aplicar ese conocimiento para modificar un sketch existente y hacerlo interactuar con un dispositivo externo. 
