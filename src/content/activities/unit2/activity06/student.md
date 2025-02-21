## Solución a la sexta actividad  
### Código que muestra en la pantalla LED del micro:bit diferentes imágenes y texto:  
``` Python
from microbit import *

# Muestra una secuencia de imágenes y texto en la pantalla delk microbit
while True:
    display.show(Image.HEART)  # Muestra un corazón
    sleep(1000)  # Espera 1 segundo
    display.show(Image.ASLEEP)  # Muestra una cara durmiendo
    sleep(1000)
    display.show("UPB")  # Muestra el texto "UPB"
    sleep(1000)
    display.show(Image.GIRAFFE)  # Muestra una jirafa
    sleep(1000)
```
