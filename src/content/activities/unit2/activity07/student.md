## Solución a la septima actividad
### Generación de sonidos utilizando el speaker o buzzer del micro:bit
``` Python
from microbit import *
import music # Necesario para generar sonidos en el micro:bit

while True: # para que el programa sea "infinito"
    if button_a.was_pressed():
        music.play(music.BA_DING) # suena music.BA_DING luego de presionar el botón A, que es un sonido corto tipo notificación
    elif button_b.was_pressed():
        music.play(music.DADADADUM) # suena music.DADADADUM luego de presionar el botón B, que es un sonido más largo con un ritmo reconocible de miedo.
```
