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

