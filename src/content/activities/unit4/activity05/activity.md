#### El momento de aplicar lo aprendido

🎯 **Enunciado**: selecciona uno de los ejemplos que exploraste en la actividad 1 y realiza 
las modificaciones necesarias para que interactúe con el micro:bit. El micro:bit estará 
ejecutando este programa:

``` py
# Imports go at the top
from microbit import *

uart.init(115200)
display.set_pixel(0,0,9)

while True:
    xValue = accelerometer.get_x()
    yValue = accelerometer.get_y()
    aState = button_a.is_pressed() 
    bState = button_b.is_pressed()
    data = "{},{},{},{}\n".format(xValue, yValue, aState,bState)
    uart.write(data)
    sleep(100) # Envia datos a 10 Hz
```

📤 **Entrega**: 

- Enlace a la aplicación original sin modificar, pero recreada en el editor de p5.js.
- Muestra el código de p5.js para la versión modificada.
- Enlace a la aplicación modificada en el editor de p5.js.
- Muestra capturas de pantalla del canvas de tu aplicación modificada.