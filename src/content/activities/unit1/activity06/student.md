## Solucion a la sexta actividad  
### ¿Qué pasa en el punto 15 y cómo crees que esto se logre?  
El computador detecta el input de los botones "A" y "B", según se presione el programa recibe el input. El código p5.js monitorea los datos 
entrantes a través del puerto serial y, dependiendo del carácter recibido ("A" o "B"), cambia el color del círculo en la pantalla de la 
aplicación y en pantalla se coloca el círculo de color rojo o amarillo respectivamente, este vendría sientdo el output. Este proceso se da 
mediante la comunicación UART, que permite el intercambio de datos entre el micro:bit y el computador en tiempo real.
### ¿Qué pasa en el punto 16 y cómo crees que esto se logre?  
Un sensor detecta un gesto de agitación y envía el carácter 'C' a través de la comunicación UART. En p5.js, cuando el programa recibe 
este carácter, cambia el color del círculo a verde. Esto funciona gracias al acelerómetro integrado en el micro:bit, que es capaz de 
detectar cambios en la aceleración y reconocer ciertos gestos predefinidos, como "shake". En el código de MicroPython, la función 
was_gesture('shake') verifica si el micro:bit ha detectado este movimiento y, si es así, envía la señal correspondiente.
### ¿Qué pasa en el punto 17 y cómo crees que esto se logre?  
Cuando se presiona el botón "Send Love" en la aplicación de p5.js el programa envía el carácter 'h' al micro:bit. En el código de 
MicroPython, se monitorea si hay datos disponibles en la comunicación UART y, si el dato recibido es 'h', la 
pantalla del micro:bit cambia para mostrar una imagen de un corazón seguido de una cara feliz. Esto se logra porque el micro:bit está 
constantemente revisando si hay datos entrantes mediante la función uart.any(), y cuando detecta el carácter 'h', ejecuta una serie de
instrucciones para cambiar la pantalla y mostrar las imágenes correspondientes.
