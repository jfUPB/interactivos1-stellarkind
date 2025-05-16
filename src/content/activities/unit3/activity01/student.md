## Solución a la primer actividad  
### Código programa de tres semáforos:  
``` python 
from microbit import *
import utime

class Semaforo:
    def __init__(self, column, red_time, yellow_time, green_time): #constructor
        self.column = column
        self.state = "Red" #Pa q todos empiecen en estado rojo
        self.startTime = utime.ticks_ms()
        self.intervals = { #Estados a sus duraciones en milisegundos, pa q al crear las instacias sea más facil expresarlas en segundos.
            "Red": red_time * 1000,
            "Yellow": yellow_time * 1000,
            "Green": green_time * 1000
        }
        self.update_display()

    def update_display(self):
        # apaga todos los LEDs de la columna especificada para evitar que leds de estados anteriores permanezcan encendidos, 
        #sin esto esta vaina se quedaban los leds encendidos de estados anteriores.
        display.set_pixel(self.column, 0, 0)
        display.set_pixel(self.column, 2, 0)
        display.set_pixel(self.column, 4, 0)
        # pa q muestre el color que es según el estado actual del semáforo
        if self.state == "Red":
            display.set_pixel(self.column, 0, 9)
        elif self.state == "Yellow":
            display.set_pixel(self.column, 2, 9)
        elif self.state == "Green":
            display.set_pixel(self.column, 4, 9)

    def update(self):
        current_time = utime.ticks_ms()
        if utime.ticks_diff(current_time, self.startTime) > self.intervals[self.state]:
            self.startTime = current_time #actualiza el estado del semáforo basándose en el tiempo transcurrido
            # Cambiamos el estado del semáforo al siguiente en el ciclo
            if self.state == "Red":
                self.state = "Green"
            elif self.state == "Green":
                self.state = "Yellow"
            elif self.state == "Yellow":
                self.state = "Red"
            self.update_display()

# Ahora las instancias de la clase Semaforo en diferentes columnas del display del micro:bit y con diferentes tiempos para cada color.
semaforo1 = Semaforo(0, 5, 2, 3)
semaforo2 = Semaforo(2, 3, 1, 2)
semaforo3 = Semaforo(4, 4, 3, 2)

while True: #bucle que constantemente actualiza los etados de los tres semáforos
    semaforo1.update()
    semaforo2.update()
    semaforo3.update()

```
### Reflexión sobre las ventajas de usar una clase y la técnica de programación basada en máquinas de estado:  
Una clase nos permite reutilizar el mismo bloque de código para crear múltiples semáforos, cada uno con sus propios tiempos y ubicación en el display, sin necesidad de escribir el código desde cero para cada uno. 
Esto no solo ahorra tiempo sino que también hace que el código sea más fácil de mantener y expandir si queremos añadir más funciones más adelante. Además, cada semáforo creado a partir de la clase puede manejar 
su propia información y comportamiento, lo que hace que el programa sea más organizado y reduce la posibilidad de errores, porque cada semáforo se preocupa solo por sus propios datos.  
En cuanto a las máquinas de estado, esta técnica de programación es muy poderosa para controlar sistemas como semáforos, donde el comportamiento cambia dependiendo del estado actual. Las máquinas de estado hacen 
muy claro y predecible cómo debe comportarse el semáforo en cualquier momento. Si algo va mal, es mucho más fácil averiguar el problema porque se puede ver en qué estado estaba el sistema y qué estaba 
supuesto a hacer después. Además, esta manera de programar asegura que el sistema use sus recursos eficientemente. Solo se realizan cambios cuando cuando hay un cambio de estado, lo cual 
es especialmente valioso en dispositivos con recursos limitados.
