#### Caso de estudio: micro:bit

Desde esta actividad hasta la fase de aplicación, te voy a guiar para que 
transformes y adaptes el caso de estudio para lograr controlar partes de este 
con el micro:bit. Primero te mostraré cómo transmitir información desde el 
micro:bit.

**Enunciado**: analiza el código del micro:bit que te permitirá enviar 
información a un sketch en p5.js.

Vas a analizar lentamente el siguiente código del micro:bit

``` py
# Imports go at the top
from microbit import *

uart.init(115200)
state = "Init"
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

- Programa el micro:bit con este código y luego abre la aplicación [SerialTerminal](https://juanferfranco.github.io/serialTerminal/) 
para ver los datos que se están enviando.

- ¿Qué información se está enviando? ¿Cómo se está enviando? 
Qué significa esta parte del código:

``` py
"{},{},{},{}\n".format(xValue, yValue, aState,bState)
```

- Observa en la aplicación SerialTerminal cómo se ven los datos que se están 
enviando. ¿Qué puedes inferir de la estructura de los datos?
- ¿Por qué se separan los datos con comas y se termina con un salto de línea?
- ¿Qué crees que pasaría si no se separan los datos con comas y no terminan con
un salto de línea?
- Para qué crees que se usa la función `sleep(100)`? ¿Qué pasaría si no se 
usara?
- Observa cómo cambian los valores de `xValue` y `yValue` a medida que el micro:bit 
se inclina hacia la izquierda, derecha, adelante y atrás. ¿Qué valores toman 
`xValue` y `yValue` en cada caso?
- ¿Qué valores toman `aState` y `bState` cuando presionas los botones A y B?
- Observa qué ocurre si en vez de is_pressed() usas was_pressed(). ¿Qué diferencias
encuentras?

Finalmente, analiza este asunto: si el micro:bit tiene los siguientes datos xValue: 969, 
yValue: 652, aState: True, bState: False ¿Qué bytes se enviarían por el puerto serial? Piensa, 
primero piensa por favor, y luego verifica con la aplicación [SerialTerminal](https://juanferfranco.github.io/serialTerminal/). 
Ten presente que en `Mostrar datos como` puedes ver los bytes que se están enviando mediante 
`Todo en HEX`.

**Entrega**: reporta los experimentos y hallazgos que vas encontrando a medida 
que analizas el código y responde las preguntas que te voy haciendo en el enunciado.
