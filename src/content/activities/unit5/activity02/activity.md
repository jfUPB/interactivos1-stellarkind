#### Caso de estudio: micro:bit

Vamos a transformar el caso de estudio de la unidad anterior para que 
ahora la comunicación entre el micro:bit y p5.js se realice mediante 
un protocolo binario. Primero analizaremos el código del micro:bit y 
en la siguiente actividad veremos cómo leer los datos en p5.js.

Durante la lectura te indicaré los momentos en los que debes detenerte 
para analizar 🧐, experimentar 🧪 y reportar ✍️ tus hallazgos en la bitácora de aprendizaje.


🎯 **Enunciado**: vamos a transformar el código

Originalmente este era el código del micro:bit que enviaba datos en texto plano o ASCII:

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

Ahora vas a reemplazar la manera como empaquetarás los datos para enviarlos 
por el puerto serial. Cambia esta línea:

``` py
data = "{},{},{},{}\n".format(xValue, yValue, aState,bState)
```

Por esta:

``` py
data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
```
Para que la línea anterior funcione deberás importar el módulo 
`struct` al inicio del código: 

``` py
# Imports go at the top
from microbit import *
import struct
```

El módulo `struct` permite empaquetar los datos en un formato binario. En este caso,  
el formato `'>2h2B'` indica que se envían 2 enteros cortos (xValue, yValue) y 2 enteros  
sin signo (aState, bState). El símbolo `>` indica que los datos se envían en orden de   
bytes grande (big-endian), lo que significa que el byte más significativo se envía primero.   
El formato `2h` indica que se envían 2 enteros cortos de 2 bytes cada uno (xValue, yValue),  
y `2B` indica que se envían 2 enteros sin signo de 1 byte cada uno (aState, bState).  
El resultado es que los datos se envían en un formato binario más compacto y eficiente  
que el formato de texto plano. Esto es especialmente útil cuando se envían grandes  
cantidades de datos o cuando se requiere un rendimiento óptimo en la comunicación.  

El código completo quedaría así:

``` py
# Imports go at the top
from microbit import *
import struct
uart.init(115200)
display.set_pixel(0,0,9)

while True:
    xValue = accelerometer.get_x()
    yValue = accelerometer.get_y()
    aState = button_a.is_pressed()
    bState = button_b.is_pressed()
    data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
    uart.write(data)
    sleep(100) # Envia datos a 10 Hz
```

¿Pero cómo se ven esos datos binarios? Para averiguarlo, vas a usar la aplicación
[SerialTerminal](https://juanferfranco.github.io/serialTerminal/) que usaste en la unidad anterior.

Abre la aplicación, configura el puerto, deja los valores por defecto y presiona ``Conectar``. Selecciona 
el puerto del micro:bit (mbed Serial port) y presiona ``Conectar``. Luego, en la sección de 
``Recepción de Datos``, en ``Mostrar datos como``, selecciona ``Texto``.

🧐🧪✍️ Captura el resultado del experimento anterior. ¿Por qué se ve este resultado?

Ahora cambia la opción de ``Mostrar datos como`` a ``Todo en Hex`` y vuelve a capturar el resultado.

🧐🧪✍️ Captura el resultado del experimento anterior. Lo que ves ¿Cómo está relacionado 
con esta línea de código?

``` py
data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
```

No te parece que el resultado es un poco más difícil de leer que el texto en ASCII?

🧐🧪✍️ ¿Qué ventajas y desventajas ves en usar un formato binario en lugar de texto en ASCII?

Ahora te voy a proponer un experimento que te permitirá ver mejor los datos. Cambia 
el código del micro:bit por este: 

``` py
# Imports go at the top
from microbit import *
import struct
uart.init(115200)
display.set_pixel(0,0,9)

while True:
    if accelerometer.was_gesture('shake'):
        xValue = accelerometer.get_x()
        yValue = accelerometer.get_y()
        aState = button_a.is_pressed()
        bState = button_b.is_pressed()
        data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
        uart.write(data)
```

🧐🧪✍️ Captura el resultado del experimento. ¿Cuántos bytes se están enviando por 
mensaje? ¿Cómo se relaciona esto con el formato `'>2h2B'`? ¿Qué significa cada uno de los bytes
que se envían?

🧐🧪✍️ Recuerda de la unidad anterior que es posible enviar números positivos y negativos 
para los valores de xValue y yValue. ¿Cómo se verían esos números en el formato
`'>2h2B'`? 

Ahora realiza el siguiente experimento para comparar el envío de datos en ASCII y en binario.

``` py
# Imports go at the top
from microbit import *
import struct
uart.init(115200)
display.set_pixel(0,0,9)

while True:
    if accelerometer.was_gesture('shake'):
        xValue = accelerometer.get_x()
        yValue = accelerometer.get_y()
        aState = button_a.is_pressed()
        bState = button_b.is_pressed()
        data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
        uart.write(data)
        uart.write("ASCII:\n")
        data = "{},{},{},{}\n".format(xValue, yValue, aState,bState)
        uart.write(data)
```

🧐🧪✍️ Captura el resultado del experimento. ¿Qué diferencias ves entre los datos 
en ASCII y en binario? ¿Qué ventajas y desventajas ves en usar un formato binario
en lugar de texto en ASCII? ¿Qué ventajas y desventajas ves en usar un formato
ASCII en lugar de binario?

📤 **Entrega**: reporta en la bitácora en todos los puntos que te marqué 
para analizar 🧐, experimentar 🧪 y reportar ✍️ tus hallazgos.