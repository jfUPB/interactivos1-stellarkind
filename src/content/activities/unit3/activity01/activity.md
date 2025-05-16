#### Volvamos a la actividad del semáforo

**ANTES DE COMENZAR** a trabajar en esta actividad, por favor, lee completamente
el enunciado.

**Enunciado**: en la actividad anterior construiste un semáforo con el micro:bit. Ahora te pediré 
que hagas una modificación. Esta vez construirás tres semáforos concurrente en el micro:bit. 

Cada uno de los semáforos tendrá unos tiempos en rojo, amarillo y verde diferentes. Recuerda 
que el micro:bit tiene un solo display de 5x5 leds. Además, todos los leds son de color rojo. 
Así que tendrás que ser creativo para representar los colores amarillo y verde.

Los tiempos para los semáforos serán los siguientes:

- Semáforo 1: 5 segundos en rojo, 2 segundos en amarillo y 3 segundos en verde.
- Semáforo 2: 3 segundos en rojo, 1 segundo en amarillo y 2 segundos en verde.
- Semáforo 3: 4 segundos en rojo, 3 segundos en amarillo y 2 segundos en verde.

La estructura de tu programa será similar a la siguiente:

``` py

class Semaforo:
    .
    .
    .

semaforo1 = Semaforo(...)
semaforo2 = Semaforo(...)
semaforo3 = Semaforo(...)

while True:
    semaforo1.update()
    semaforo2.update()
    semaforo3.update()
```

- Qué ventajas tiene usar una **clase (class)** en este caso para representar 
un semáforo?

**Entrega**: el código de tu programa. Y una reflexión sobre las ventajas de usar 
una clase en este caso y la técnica de programación basada en máquinas de estado.