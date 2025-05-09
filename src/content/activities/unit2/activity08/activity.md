####  Analizando un Programa con Máquinas de Estados

**Enunciado**: analiza el siguiente código identificando estados, eventos y acciones. Responde las preguntas planteadas.

``` py

from microbit import *
import utime

class Pixel:
    def __init__(self,pixelX,pixelY,initState,interval):
        self.state = "Init"
        self.startTime = 0
        self.interval = interval
        self.pixelX = pixelX
        self.pixelY = pixelY
        self.pixelState = initState

    def update(self):

        if self.state == "Init":
            self.startTime = utime.ticks_ms()
            self.state = "WaitTimeout"
            display.set_pixel(self.pixelX,self.pixelY,self.pixelState)

        elif self.state == "WaitTimeout":
            if utime.ticks_diff(utime.ticks_ms(),self.startTime) > self.interval:
                self.startTime = utime.ticks_ms()
                if self.pixelState == 9:
                    self.pixelState = 0
                else:
                    self.pixelState = 9
                display.set_pixel(self.pixelX,self.pixelY,self.pixelState)

pixel1 = Pixel(0,0,0,1000)
pixel2 = Pixel(4,4,0,500)

while True:
    pixel1.update()
    pixel2.update()

```

- Describe detalladamente cómo funciona este ejemplo. Usa chatGPT para indagar y profundizar en todas las dudas que puedas tener.
- Del contexto del programa ¿Puedes identificar algún estado? Los estados son momentos del programa en los cuales este espera a que ocurra algo.
- Del contexto del programa ¿Puedes identificar algún evento? Los eventos son aquellas cosas 
por las que el programa pregunta durante un estado.
- Del contexto del programa ¿Puedes identificar alguna acción? Las acciones son aquellas cosas 
que el programa ejecuta en respuesta a la ocurrencia de un evento.

**Entrega**: respuesta a las preguntas.
