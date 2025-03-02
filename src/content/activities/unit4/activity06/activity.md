#### Consolidación de lo aprendido

Esta parte del proceso de aprendizaje es fundamental. Trata de hacerla de memoria, sin consultar las 
notas en la bitácora, y luego compara tus respuestas con tus notas. En términos de aprendizaje, 
este ejercicio se llama recuperación y es muy efectivo para fijar el conocimiento en la memoria a largo plazo. 

**Recuerda**: trata de ser lo más específico posible en tus respuestas. Esta no es una actividad de relleno, 
sino una oportunidad para reflexionar sobre lo que has aprendido y fijar en memoria de largo plazo los conceptos
y técnicas aprendidas.

**Enunciado**: en esta actividad vas a recordar los conceptos y técnicas aprendidas en esta unidad. Para 
ello te pediré que contestes en tu bitácora las siguientes preguntas y justifica lo que dices 
mostrando solo fragmentos de código de tus actividades donde estés aplicando los conceptos y técnicas.

1. ¿Qué es un protocolo de comunicación y por qué es importante en la comunicación serial?
2. ¿Por qué se separan los datos con comas en el protocolo ASCII que exploramos?
3. ¿Por qué es necesario terminar los datos con un carácter que marque el fin del mensaje?
4. ¿Por qué fue necesario usar una máquina de estados en la aplicación modificada de p5.js?
5. ¿Cómo se formatean los datos en el micro:bit para ser enviados por el puerto serial?
6. ¿Qué significa que los datos enviados por el micro:bit están codificados en ASCII?
7. ¿Por qué es necesario en la aplicación de p5.js preguntar si hay bytes disponibles en el puerto serial antes de leerlos?

```javascript	
if (port.availableBytes() > 0) {
    let data = port.readUntil("\n");
```
¿Qué pasa si esto no se hace?  
8. ¿Cómo se elimina el retorno de carro o salto de línea de un string en p5.js?  
9. Si una cadena está tiene información separada por espacios y quieres separar la información en 
varias cadenas individuales ¿Qué función de p5.js usarías?  
10. Por qué es necesario en la aplicación del caso de estudio convertir las cadenas 
a números enteros antes de usarlas en el sketch de p5.js?  
11. Si el micro:bit tiene los siguientes datos xValue: 123, yValue: 756, aState: False, bState: True 
¿Qué bytes se enviarían por el puerto serial?  
12. ¿Qué aprendiste nuevo del micro:bit que no sabías antes?  
13. ¿Qué aprendiste nuevo de p5.js que no sabías antes?  

**Entrega**: responde estas preguntas en tu bitácora y justifica tus respuestas mostrando fragmentos de código.
