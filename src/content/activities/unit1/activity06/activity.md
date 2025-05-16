#### Herramientas y tecnologías

**Enunciado**: en este curso vas a explorar los conceptos usando principalmente dos herramientas: 
[micro:bit](https://microbit.org/) y [p5.js](https://p5js.org/). En esta actividad vas a 
conocer estas herramientas implementando tu primer sistema físico interactivo. Ten presente 
que esta actividad es introductoria y no busca que domines estas herramientas. Vas a seguir 
los siguientes pasos:

1. Abre una versión actualizada de Google chrome o Microsogt edge.
2. Abre en una pestaña este sitio [micro:bit editor](https://python.microbit.org/v/3).
3. Copia en el editor el siguiente código:

``` py
from microbit import *

uart.init(baudrate=115200)
display.show(Image.BUTTERFLY)

while True:
    if button_a.is_pressed():
        uart.write('A')
        sleep(500)
    if button_b.is_pressed():
        uart.write('B')
        sleep(500)
    if accelerometer.was_gesture('shake'):
        uart.write('C')
        sleep(500)
    if uart.any():
        data = uart.read(1)
        if data:
            if data[0] == ord('h'):
                display.show(Image.HEART)
                sleep(500)
                display.show(Image.HAPPY)
```

4. Conecta el micro:bit a un puerto USB del computador.
5. Al lado del botón Send to micro:bit hay **tres puntos**. Selecciona Connect.
6. Selecciona el puerto USB lógico asignado por el sistema operativo al micro:bit. En este
   caso **mbed Serial Port**.
7. Si no hay errores. Presiona **Send to micro:bit** y espera que aparezca una mariposa en 
el display de este. Eso quiere decir que el programa anterior ya estará cargado en la memoria 
del micro:bit.
8. Ahora abre en otra pestaña el [editor de p5.js](https://p5js.org/).
9. Observa que al lado del nombre del archivo: sketch.js hay una flecha. Si das click 
podrás observar tres archivos: index.html, sketch.js, style.css.
10. Abre el archivo index.html e incluye la siguiente línea antes de la tag **link**. Esta línea 
permite incluir una biblioteca que facilita la comunicación USB serial entre el computador 
y el micro:bit.

``` js
<script src="https://unpkg.com/@gohai/p5.webserial@^1/libraries/p5.webserial.js"></script>
```
11. Ahora en el archivo sketch.js copia el siguiente código:

``` js

let port;
let connectBtn;

function setup() {
    createCanvas(400, 400);
    background(220);
    port = createSerial();
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);
    let sendBtn = createButton('Send Love');
    sendBtn.position(220, 300);
    sendBtn.mousePressed(sendBtnClick);
    fill('white');
    ellipse(width / 2, height / 2, 100, 100);
}

function draw() {

    if(port.availableBytes() > 0){
        let dataRx = port.read(1);
        if(dataRx == 'A'){
            fill('red');   
        }
        else if(dataRx == 'B'){
            fill('yellow'); 
        }
        else{
            fill('green'); 
        }
        background(220);
        ellipse(width / 2, height / 2, 100, 100);
        fill('black');
        text(dataRx, width / 2, height / 2);
    }    


    if (!port.opened()) {
        connectBtn.html('Connect to micro:bit');
    } 
    else {
        connectBtn.html('Disconnect');
    }
}

function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200);
    } else {
        port.close();
    }
}

function sendBtnClick() {
    port.write('h');
}

```

12. Dale click a Play sketch.
13. Dale ahora click a Connect to micro:bit
14. Selecciona el puerto mbed Serial Port.
15. Presiona los botones A y B del micro:bit.
16. Sacude el micro:bit. ¿Qué pasa?
17. Presiona el botón Send Love. ¿Qué pasa?

**Entrega**: vas a tratar de analizar todos los pasos anteriores usando 
los conocimientos que tienes hasta este punto de la carrera. No te asustes 
con esta actividad, la idea es que intentes, estamos explorando y en las próximas 
unidades vas a poder entender muy bien todo. Por ahora, solo quiero que intentes.

1. Describe qué pasa en el punto 15 y cómo crees que esto se logre.
2. Describe qué pasa en el punto 16 y cómo crees que esto se logre.
3. Describe qué pasa en el punto 17 y cómo crees que esto se logre.