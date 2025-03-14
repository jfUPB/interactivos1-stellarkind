##  Solución a la décima actividad   
### Explicación Concurrencia:  
En un sistema como el micro:bit, no existe multitarea real (no hay hilos corriendo en paralelo, según el internet), pero aquí simulan la concurrencia con un bucle principal (```while True:```) que se ejecuta muy 
rápidamente y en cada iteración hace lo siguiente:  
  - Verifica eventos de entrada (tipo si el botón A fue presionado).  
  - Verifica eventos de tiempo (por ejemplo, si ya pasó el intervalo para cambiar la imagen).  
Esto hace que el programa pueda responder casi inmediatamente a una pulsación de botón, sin perder de vista el paso del tiempo para cambiar de estado cuando corresponde. 
Cada estado (HAPPY, SMILE, SAD) define qué imagen se muestra y cuánto dura antes de la transición. Si en algún momento se detecta la pulsación del botón, se puede “interrumpir” el tiempo restante y pasar
a otro estado de manera inmediata. De esta forma, se gestionan dos cosas a las vez:  
  - El tiempo, que decide la transición natural (p. ej. de HAPPY a SMILE).  
  - El botón, que desencadena una transición inmediata (p. ej. de HAPPY a SAD).  
Esta estructura de máquina de estados dentro de un bucle continuo es lo que permite la concurrencia en este contexto.   
### Vectores de Prueba:
### - **Vector 1)** Secuencia Natural sin Pulsaciones 
  - **Condiciones Iniciales:** El programa inicia en ```STATE_INIT```.  
  - **Eventos:** No se presiona ningún botón. Se deja transcurrir el tiempo suficiente para que complete al menos un ciclo entero.  
  - **Resultados Esperados:**  
    - ```STATE_INIT``` → muestra HAPPY, configura intervalo = 1500 ms → pasa a ```STATE_HAPPY```.   
    - Tras 1.5 s, pasa a ```STATE_SMILE``` (muestra SMILE, intervalo = 1000 ms).   
    - Tras 1 s, pasa a ```STATE_SAD``` (muestra SAD, intervalo = 2000 ms).  
    - Tras 2 s, regresa a ```STATE_HAPPY``` y se repite el ciclo.  
  - **Resultados Obtenidos:**   
    - _**El micro:bit efectivamente muestra la secuencia**_ HAPPY → SMILE → SAD → HAPPY con los intervalos esperados.  
    - **Conclusión:** La prueba se supera si la secuencia y los tiempos coinciden con lo esperado.  
### - **Vector 2)** Pulsación del Botón A en “HAPPY”  
  - **Condiciones Iniciales:** El sistema se encuentra en ```STATE_HAPPY``` (cara HAPPY en pantalla). El tiempo transcurrido es menor a 1500 ms (no se cumplió el intervalo).
  - **Eventos:** Se presiona el botón A.  
  - **Resultados Esperados:**  
    - Según la lógica, al detectar ```button_a.was_pressed()```, se dispara la transición a ```STATE_SAD```:
        - Muestra SAD inmediatamente.
        - Asigna ```start_time = utime.ticks_ms()``` e ```interval = SAD_INTERVAL (2000 ms)```.
        - Cambia a ```STATE_SAD```
    - Luego, tras 2 s, vuelve a HAPPY según la lógica normal.  
  - **Resultados Obtenidos:**  
    - Se verifica que la cara cambie de HAPPY a SAD en el momento de la pulsación, sin esperar a que se cumpla el intervalo de 1.5 s.
    - **Conclusión:** Si se observa el cambio inmediato a SAD y, pasado el tiempo correspondiente, vuelve a HAPPY, la prueba se supera.
### - **Vector 3)** Pulsación del Botón A en “SAD”  
  - **Condiciones Iniciales:** El programa ha avanzado hasta ```STATE_SAD``` (cara SAD en pantalla). El tiempo transcurrido es menor a 2000 ms (no se cumplió el intervalo).
  - **Eventos:** Se presiona el botón A.  
  - **Resultados Esperados:**  
    - Según la lógica, al presionar el botón A en ```STATE_SAD```, el programa:
        - Muestra SMILE de inmediato.
        - Cambia a ```STATE_SMILE```.
        - Asigna ```start_time = utime.ticks_ms()``` e ```interval = SMILE_INTERVAL (1000 ms)```.
    - Tras 1 s, se evalúa el tiempo y transita a ```STATE_SAD``` nuevamente (o a HAPPY si se presiona el botón antes), de acuerdo con la programación.  
  - **Resultados Obtenidos:**  
    - Se verifica que la cara cambie de SAD a SMILE inmediatamente tras pulsar A, y luego continúe su flujo normal.
    - **Conclusión:** Si se observa la transición inmediata y la posterior secuencia de tiempos, la prueba se supera.

