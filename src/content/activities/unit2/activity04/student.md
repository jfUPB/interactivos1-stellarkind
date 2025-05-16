## Solución a la cuarta actividad  
### Experimentos manipulando Entradas Digitales.    
**¿Qué quería comprobar?**
_Quería verificar si el micro:bit puede detectar y responder correctamente a diferentes entradas, mostrando símbolos específicos en la pantalla LED al presionar los botones A, B, A+B juntos y el logo táctil._
**¿Cuál fue mi hipótesis?** _Si utilizo is_pressed() para los botones A y B y is_touched() para el logo, entonces:_  
- Presionar A mostrará la letra "A" en la pantalla.  
- Presionar B mostrará la letra "B" en la pantalla.   
- Presionar A y B al mismo tiempo mostrará un corazón ❤️.
- Tocar el logo táctil mostrará una jirafa 🦒.   
- Si no se presiona nada, la pantalla debería quedar en blanco.

#### **Código #1 del experimento:**  
``` python
from microbit import *

while True:
    if button_a.is_pressed():
        display.show('A')
    if button_b.is_pressed():
        display.show('B')
    if pin_logo.is_touched():
        display.show(Image.GIRAFFE)
    if button_a.is_pressed() and button_b.is_pressed():
        display.show(Image.HEART)
        
    else:
        display.clear()
```
**Después de probar el código, se observaron los siguientes comportamientos:** 
- Presionar el botón A muestra la letra "A", pero si B también está presionado, cambia a "B" o a un corazón rápidamente. 
- Presionar el botón B muestra la letra "B", pero si A también está presionado, cambia de forma errática.  
- Tocar el logo táctil muestra la imagen de la jirafa, pero puede cambiar rápidamente si se presiona otro botón al mismo tiempo.  
- Presionar A y B juntos muestra el corazón, pero el código aún revisa otras condiciones, por lo que la imagen puede parpadear o cambiar bruscamente.  
- La pantalla parpadea rápidamente cuando no se presiona nada, ya que display.clear() se ejecuta en cada ciclo del while True.
  
**Análisis de los resultados:**
El problema principal es que todas las condiciones se evalúan en cada iteración sin pausas. Y como el código se ejecuta muy rápido, si dos condiciones se cumplen casi al mismo tiempo, la pantalla cambia de imagen repetidamente como indescisa. Por eso el if de A y B juntos debería ir primero, ya que si ambos botones están presionados, el código aún ejecuta los if anteriores antes de llegar a esa condición. Aparte debería poner un pequeño sleep() después de cada if para evitar que la pantalla parpadee constantemente al cambiar entre imágenes.  
  
#### **Código #2 del experimento:**  
``` python
from microbit import *

while True:

    if button_a.is_pressed() and not button_b.is_pressed():
        display.show('A')
        sleep(200)

    if button_b.is_pressed() and not button_a.is_pressed():
        display.show('B')
        sleep(200)

    if pin_logo.is_touched():
        display.show(Image.GIRAFFE)
        sleep(200)

    if button_a.is_pressed() and button_b.is_pressed():
        display.show(Image.HEART)
        sleep(200)

    else:
        display.clear()
        sleep(200)
```
**Después de probar el código, se observaron los siguientes comportamientos:**  
- Presionar A, muestra la letra "A", pero hay un leve retraso si se presiona justo después de otro botón.  
- Presionar B, muestra la letra "B", sin interferencias con A.  
- Tocar el logo táctil, muestra correctamente la jirafa, pero si otro botón se presiona justo después, la imagen puede cambiar rápidamente.  
- Presionar A y B juntos, muestra correctamente el corazón , aunque hay un pequeño conflicto con la limpieza de la pantalla en algunos casos.  
- Cuando no se presiona nada, la pantalla se limpia como se espera.
  
**Análisis de los resultados:**
El código presenta algunos problemas debido al orden en que se evaluaban las condiciones y al uso innecesario de verificaciones adicionales. La detección de A y B juntos estaba al final, lo que provocaba que, al presionarlos simultáneamente, primero se mostrara “A” o “B” antes de cambiar al corazón, generando un efecto de parpadeo. Además, la inclusión de and not button_X.is_pressed() era redundante, ya que cada condición ya se evaluaba de forma exclusiva. El uso de sleep(200) dentro de cada if ralentizaba la detección de los botones, haciendo que la respuesta pareciera más lenta. Finalmente, la ejecución constante de else: display.clear() causaba que la pantalla se apagara inmediatamente cuando no había una entrada activa, generando parpadeo en la transición de imágenes. Ahora probamos cambiando los if por elif, no escribiendo and not button_X.is_pressed() y cambiando el orden de las instrucciones.  
  
#### **Código final del experimento:**
``` python
from microbit import *

while True:
    if button_a.is_pressed() and button_b.is_pressed():
        display.show(Image.HEART)  # Muestra un corazón si A y B están presionados juntos
    elif button_a.is_pressed():
        display.show('A')  # Muestra "A" si se presiona el botón A
    elif button_b.is_pressed():
        display.show('B')  # Muestra "B" si se presiona el botón B
    elif pin_logo.is_touched():
        display.show(Image.GIRAFFE)  # Muestra una jirafa si se toca el logo
    else:
        display.clear()  # Borra la pantalla si no se presiona nada
```
**Descripción de los resultados:**   
- Presionar A mostró correctamente la letra "A" en la pantalla LED.  
- Presionar B mostró la letra "B" en la pantalla LED.  
- Presionar A y B juntos mostró el corazón, sin interferencias con las otras detecciones.  
- Tocar el logo del micro:bit mostró correctamente la imagen de la jirafa.  
- Al soltar todos los botones, la pantalla quedó vacía como se esperaba.
  
**Análisis de los resultados:**  
- El código detecta correctamente cada entrada, asignando una respuesta específica a cada una.  
- La detección del logo táctil es más lenta que la de los botones físicos, ya que funciona por capacitancia en lugar de contacto directo.  
- La condición if button_a.is_pressed() and button_b.is_pressed() tiene prioridad sobre los casos individuales de A y B, lo que evita conflictos entre las lecturas.  
- La función display.clear() asegura que la pantalla se borre solo cuando no hay entradas activas, manteniendo el comportamiento esperado.

#### Conclusiones:  
A lo largo de los experimentos, se pudo confirmar que el micro:bit detecta correctamente cada entrada, mostrando símbolos específicos en la pantalla LED en respuesta a la interacción del usuario. La hipótesis inicial fue acertada, ya que cada botón (A, B, A+B y el logo) generó la salida esperada. Sin embargo, el orden en que se ejecutaban las condiciones tuvo un impacto directo en la estabilidad del sistema. En versiones previas del código, el uso de if en todas las condiciones causó cambios erráticos en la pantalla, especialmente al presionar A y B juntos, ya que se evaluaban individualmente antes de llegar a la condición que mostraba el corazón. Además, el uso de sleep(200) dentro de cada if ralentizaba la respuesta del micro:bit, haciendo que la detección de las entradas pareciera menos fluida. También se evidenció que el botón táctil del logo responde más lentamente que los botones físicos, lo que lo hace más adecuado para funciones secundarias o complementarias.  
Gracias a estas pruebas, se logró mejorar la estructura del código, reorganizando el orden de las condiciones y reemplazando los if individuales por elif para evitar múltiples ejecuciones en un solo ciclo. Esto permitió que la pantalla respondiera de manera estable y sin parpadeos innecesarios.
