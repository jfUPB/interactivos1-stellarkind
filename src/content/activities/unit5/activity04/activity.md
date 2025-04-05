#### Aplica lo aprendido

🎯 **Enunciado**: vas a modificar la misma aplicación de la fase de aplicación de la 
unidad anterior para que soporte el protocolo de datos binarios. La aplicación 
del micro:bit debe ser la misma que usaste en la actividad anterior:

``` py
from microbit import *
import struct

uart.init(115200)
display.set_pixel(0, 0, 9)

while True:
    xValue = accelerometer.get_x()
    yValue = accelerometer.get_y()
    aState = button_a.is_pressed()
    bState = button_b.is_pressed()
    # Empaqueta los datos: 2 enteros (16 bits) y 2 bytes para estados
    data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
    # Calcula un checksum simple: suma de los bytes de data módulo 256
    checksum = sum(data) % 256
    # Crea el paquete con header, datos y checksum
    packet = b'\xAA' + data + bytes([checksum])
    uart.write(packet)
    sleep(100)  # Envía datos a 10 Hz
```

📤 **Entrega**:

- Enlace a la aplicación original sin modificar, pero recreada en el editor de p5.js (esto 
ya lo tienes de la unidad anterior).
- Muestra el código de p5.js para la versión modificada.
- Enlace a la aplicación modificada en el editor de p5.js.
- Muestra capturas de pantalla del canvas de tu aplicación modificada.
