## Solución a la decimotercer actividad
### Reflexión sobre el diseño de la máquina de estados:
Crear la bomba temporizada fue un desafío interesante. Al final tomée la decisión de estructurar el sistema en tres estados principales: configuración, cuenta regresiva y explosión, 
para manejar claramente cada fase del proceso. Lograr que todo funcionara en el orden correcto no fue fácil, especialmente porque necesitaba que la detección de botones y el gesto de agitar 
estuvieran perfectamente sincronizados con el temporizador. Utilizar los diagramas de estados me ayudó mucho en este aspecto. Me permitieron planear y visualizar cómo se comportaría el 
sistema en cada transición, evitando errores y optimizando el código antes de siquiera empezar a programar. Aunque tuve problemas con el rebote de los botones y en manejar varias entradas 
al mismo tiempo, los diagramas me facilitaron mucho las cosas. Para mejorar, consideraría hacer el código aún más modular y tal vez usar técnicas más avanzadas, como interrupciones, para 
hacer el sistema más eficiente y fácil de manejar.
