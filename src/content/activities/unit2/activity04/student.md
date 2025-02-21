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
  
**Análisis del Problema**
El problema principal es que todas las condiciones se evalúan en cada iteración sin pausas. Y como el código se ejecuta muy rápido, si dos condiciones se cumplen casi al mismo tiempo, la pantalla cambia de imagen repetidamente como indescisa. Por eso el if de A y B juntos debería ir primero, ya que si ambos botones están presionados, el código aún ejecuta los if anteriores antes de llegar a esa condición. Aparte debería poner un pequeño sleep() después de cada if para evitar que la pantalla parpadee constantemente al cambiar entre imágenes.  
  
**Código final del experimento:**
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
