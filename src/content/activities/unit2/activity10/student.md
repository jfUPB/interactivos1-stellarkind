##  Solución a la décima actividad   
### Explicación Concurrencia:  
En un sistema como el micro:bit, no existe multitarea real (no hay hilos corriendo en paralelo, según el internet), pero aquí simulan la concurrencia con un bucle principal (```while True:```) que se ejecuta muy 
rápidamente y en cada iteración hace lo siguiente:  
    - Verifica eventos de entrada (tipo si el botón A fue presionado).  
    - Verifica eventos de tiempo (por ejemplo, si ya pasó el intervalo para cambiar la imagen).  
Esto hace que el programa pueda responder casi inmediatamente a una pulsación de botón, sin perder de vista el paso del tiempo para cambiar de estado cuando corresponde. 
Cada estado (HAPPY, SMILE, SAD) define qué imagen se muestra y cuánto dura antes de la transición. Si en algún momento se detecta la pulsación del botón, se puede “interrumpir” el tiempo restante y pasar
a otro estado de manera inmediata. De esta forma, se gestionan dos cosas a las vez:  
    -  El tiempo, que decide la transición natural (p. ej. de HAPPY a SMILE).  
    - El botón, que desencadena una transición inmediata (p. ej. de HAPPY a SAD).  
Esta estructura de máquina de estados dentro de un bucle continuo es lo que permite la concurrencia en este contexto.   
