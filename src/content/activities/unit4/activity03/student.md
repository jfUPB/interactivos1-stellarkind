## Solución a la tercer actividad:
Para empezar, cargué el código en el micro:bit. Vi que el LED en la esquina superior izquierda (el píxel 0,0) se encendió, eso me confirmó que el programa estaba corriendo. Luego, abrí la aplicación Serial Terminal en mi portátil para ver qué datos estaba enviando el micro:bit a través del cable USB.

### ¿Qué información se está enviando? ¿Cómo se está enviando? Qué significa esta parte del código: ```"{},{},{},{}\n".format(xValue, yValue, aState,bState)```

#### ¿Qué información se está enviando? y ¿Qué significa esa parte del código?
Observé en la Serial Terminal que mi micro:bit estaba enviando líneas de texto continuamente. Cada línea contenía cuatro datos separados por comas. Por lo que veo en el código es la forma en que se construye la cadena de texto que se va a enviar, infiriendo del experimento:
- El primer dato es el valor del acelerómetro en el eje X (```xValue```).
- El segundo dato es el valor del acelerómetro en el eje Y (```yValue```).
- El tercer dato es el estado del botón A (```aState```).
- El cuarto dato es el estado del botón B (```bState```).
Así que, en esencia, está enviando información sobre cómo está inclinado y si los botones están siendo presionados en ese momento.

#### ¿Cómo se está enviando?
La información se está enviando como una cadena de texto a través de la conexión serial USB. El código usa ```uart.write(data)``` para enviar la cadena ```data``` que se crea en cada ciclo del bucle principal.

### Observa en la aplicación SerialTerminal cómo se ven los datos que se están enviando. ¿Qué puedes inferir de la estructura de los datos? ¿Por qué se separan los datos con comas y se termina con un salto de línea? 
Inferí que esta estructura es para organizar los datos.La estructura era claramente cuatro valores separados por comas en cada línea, y cada línea terminaba y la siguiente empezaba en una nueva fila, donde: Las comas actúan como "separadores" entre cada tipo de dato (X, Y, Botón A, Botón B) dentro de un mismo paquete de información. El salto de línea (```\n```) marca el final de ese paquete completo.

### ¿Qué crees que pasaría si no se separan los datos con comas y no terminan con un salto de línea?
Si no se separaran con comas, los valores se verían pegados, algo como 969652FalseFalse. Sería imposible saber dónde termina el valor X del acelerómetro y dónde empieza el valor Y, o dónde empiezan los estados de los botones. El programa que reciba esto no podría interpretar los datos correctamente.
Si no terminaran con un salto de línea, el programa receptor seguiría leyendo datos indefinidamente, sin saber cuándo tiene un conjunto completo de X, Y, estado A y estado B. Podría leer una parte de una lectura y una parte de la siguiente, mezclando los datos y volviéndolos inservibles.

### ¿Para qué crees que se usa la función sleep(100)? ¿Qué pasaría si no se usara?
La función ```sleep(100)``` detiene la ejecución del programa por 100 milisegundos en cada ciclo del bucle ```while True```. Esto significa que el micro:bit envía un conjunto de datos, espera 100 ms, envía otro conjunto, espera otros 100 ms, y así sucesivamente. Un retardo de 100 ms resulta en 10 envíos de datos por segundo (1 segundo / 0.1 segundos por envío = 10 Hz).

Creo que se usa para controlar la velocidad a la que se envían los datos. No es necesario ni eficiente enviar datos tan rápido como el micro:bit pueda procesarlos. Si no se usara ```sleep(100)```, el micro:bit enviaría datos a la máxima velocidad posible. Esto podría generar varios problemas.

### Observa cómo cambian los valores de xValue y yValue a medida que el micro:bit se inclina hacia la izquierda, derecha, adelante y atrás. ¿Qué valores toman xValue y yValue en cada caso?
- Cuando lo incliné hacia la derecha (el borde con los pines 0, 1, 2 hacia abajo), ```xValue``` se volvió un valor positivo, acercándose a alrededor de 1000.
- Cuando lo incliné hacia la izquierda (el borde con los pines 0, 1, 2 hacia arriba), ```xValue``` se volvió un valor negativo, acercándose a alrededor de -1000.
- Cuando lo incliné hacia adelante (el borde con el conector USB hacia abajo), ```yValue``` se volvió un valor positivo, acercándose a alrededor de 1000.
- Cuando lo incliné hacia atrás (el borde con el conector USB hacia arriba), ```yValue``` se volvió un valor negativo, acercándose a alrededor de -1000.
- Los valores intermedios dependían de cuánto lo inclinaba. Se mueven en un rango aproximado de -1024 a +1024.

### ¿Qué valores toman aState y bState cuando presioas los botones A y B?
- Cuando presionaba y manteía el botón A, ```aState``` mostraba ```True```, al soltaelo mostraba ```False```.
- Al presionar y mantener el botón B, ```bState``` mostraba ```True```, al soltarlo mostraba ```False```.
- Si no presionaba ninguno, ambos mostraban ```False```.

### Observa qué ocurre si en vez de is_pressed() usas was_pressed(). ¿Qué diferencias encuentras?
La diferencia principal es que ```is_pressed()``` me da el estado actual (presionado o no), mientras que ```was_pressed()``` me dice si hubo una pulsación completa (presionar y soltar) desde la última vez que lo verifiqué. ```is_pressed()``` es mejor para acciones continuas o verificar si algo está presionado ahora; ```was_pressed()``` es mejor para detectar un "evento" de clic.

- Con ```is_pressed()```, ```aState``` o ```bState``` mostraban ```True``` continuamente mientras mantenía presionado el botón.
- Con ```was_pressed()```, ```aState``` o ```bState``` mostraban ```True``` solo por un instante justo después de soltar el botón si lo había presionado. Si mantenía el botón presionado, seguía viendo ```False``` hasta que lo soltaba, y entonces aparecía ```True``` en una o dos lecturas antes de volver a ```False```.

### Si el micro:bit tiene los siguientes datos ```xValue: 969, yValue: 652, aState: True, bState: False``` ¿Qué bytes se enviarían por el puerto serial?
Pensé en cómo se forma la cadena ```"{},{},{},{}\n".format(969, 652, True, False)``` resultaría en la cadena de texto ```"969,652,True,False\n"```. Para enviar eso por serial, cada carácter de esta cadena se convierte en uno o más bytes según la codificación de caracteres. Para verlo en HEX, cada carácter se convierte a su valor hexadecimal ASCII.
Calculé los valores HEX para cada carácter de "969,652,True,False\n":
| Lista de valores HEX |
|----------------------|
| '9': 39 |
| '6': 36 |
| '9': 39 |
| ',': 2C |
| '6': 36 |
| '5': 35 |
| '2': 32 |
| ',': 2C |
| 'T': 54 |
| 'r': 72 |
| 'u': 75 |
| 'e': 65 |
| ',': 2C |
| 'F': 46 |
| 'a': 61 |
| 'l': 6C |
| 's': 73 |
| 'e': 65 |
| '\n': 0A |  

Entonces, mi predicción es que los bytes enviados en formato HEX serían así:
```39 36 39 2C 36 35 32 2C 54 72 75 65 2C 46 61 6C 73 65 0A```
Verifiqué esto con la aplicación SerialTerminal configurada para mostrar "Todo en HEX" y estaba bien.
