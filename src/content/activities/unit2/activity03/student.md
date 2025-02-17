## Solución a la tercer actividad
### Algunas entradas y salidas del micro:bit:
Entradas:
- **Botones A y B:** Dos botones físicos en la parte frontal que pueden detectar pulsaciones individuales o simultáneas.
- **Sensor de luz:** Mide la cantidad de luz ambiental que incide sobre el dispositivo.
- **Acelerómetro:** Detecta movimientos y gestos, como sacudidas, inclinaciones y caídas libres.
- **Sensor de temperatura:** Permite captar la temperatura en el aire del entorno.  

Salidas:
- **Pantalla LEDs 5x5:** Una pantalla de 25 LEDs que puede mostrar imágenes, texto y patrones.
- **Mini parlante:** Este emite sonidos y tocar melodías.
- **Pines de salida:** Conectores que permiten controlar dispositivos externos como motores, luces y otros.
- **Comunicación por radio y Bluetooth:** Facilita la comunicación inalámbrica entre micro:bits o con otros dispositivos compatibles.

### Algunas funciones de Micropython para entradas y para salidas:
####Funciones de entrada:  
- **Leer botones:** Detecta si un botón ha sido presionado.  
  Función: _button_a.is_pressed()_
  Ejemplo:
  ``` python
  from microbit import *
  if button_a.is_pressed():
    display.show('A')
  ```
  Este código muestra la letra 'A' en la pantalla del micro:bit si el botón A está siendo presionado.
  
- **Detección de movimientos:** Identifica gestos como sacudir o inclinar el dispositivo.  
  Función: _accelerometer.was_gesture('shake')_
  Ejemplo:
  ``` python
  from microbit import *
  if accelerometer.was_gesture('shake'):
    display.show(Image.SURPRISED)
  ```
  Este código muestra una cara de sorpresa en la pantalla del micro:bit si se detecta que el micro:bit ha sido sacudido.  

- **Medición de la temperatura:** Obtiene la temperatura aproximada del entorno.  
  Función: _temperature()_  
  Ejemplo:  
  ``` python
  from microbit import *
  temp = temperature()
    display.scroll(str(temp))
  ```
  Este código mide la temperatura y la muestra en la pantalla.  
  
#### Funciones de salida:  
- **Mostrar imágenes en la pantalla de LEDs:** Muestra imágenes predefinidas o personalizadas.
  Función: _display.show()_  
  Ejemplo:  
  ``` python
  from microbit import *
  display.show(Image.HEART)
  ```
  Este código muestra un corazón en la pantalla del micro:bit.  
- **Encender o apagar un pin digital:** Controla dispositivos externos conectados a los pines.  
  Función: _pin0.write_digital(value)_
  Ejemplo:
  ``` python
  from microbit import *
  pin0.write_digital(1) 
  sleep(1000)           
  pin0.write_digital(0) 
  ```
  Este código enciende un dispositivo conectado al pin 0 durante un segundo y luego lo apaga.
  
- **Reproducir sonidos (solo en micro:bit V2):** Emite tonos o melodías a través del zumbador incorporado.
  Función: _music.play()_
  Ejemplo:
  ``` python
  from microbit import *
  import music
  music.play(music.PUNCHLINE)
  ```
  Este código reproduce una melodía corta utilizando el parlantito del micro:bit V2.
