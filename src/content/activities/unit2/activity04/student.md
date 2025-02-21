## Solución a la cuarta actividad  
### Experimentos manipulando Entradas Digitales.  
### Experimento 1:  
**¿Qué quería comprobar?**
_Quería verificar si el micro:bit puede detectar y responder correctamente a diferentes entradas, mostrando símbolos específicos en la pantalla LED al presionar los botones A, B, A+B juntos y el logo táctil._
**¿Cuál fue mi hipótesis?** _Si utilizo is_pressed() para los botones A y B y is_touched() para el logo, entonces:_  
- Presionar A mostrará la letra "A" en la pantalla.  
- Presionar B mostrará la letra "B" en la pantalla.   
- Presionar A y B al mismo tiempo mostrará un corazón ❤️.
- Tocar el logo táctil mostrará una jirafa 🦒.   
- Si no se presiona nada, la pantalla debería quedar en blanco.  
**Código final del experimento:**  
``` phyton
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
