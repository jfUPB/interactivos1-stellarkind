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
### Video
<video width="640" height="360" controls>
  <source src="https://github.com/jfUPB/interactivos1-stellarkind/blob/main/src/assets/SFIAct0206completada%20-%20Hecho%20con%20Clipchamp_1740176036380.mp4" type="video/mp4">
  Tu navegador no soporta el elemento de video.
</video>
