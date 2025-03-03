## Solución a la secunda actividad
### Código de la bomba 2.0:
``` python
from microbit import *
import utime
import music

# estados
STATE_CONFIG = 0       # Modo de configuración (bomba desarmada)
STATE_COUNTDOWN = 1    # Modo de cuenta regresiva (bomba armada)
STATE_EXPLODED = 2     # Estado de explosión

config_time = 20  #Valor que es configurable
current_state = STATE_CONFIG
#para la cuenta regresiva
countdown = config_time
last_tick = utime.ticks_ms()

# Secuencia de desactivación y contador de pasos
deactivation_sequence = ['button_a', 'button_b', 'button_a', 'shake']
current_step = 0

while True:
    if current_state == STATE_CONFIG:
        display.scroll(str(config_time)) # Se muestra el tiempo actual configurado.
        if button_a.was_pressed(): # Botón A: Incrementa el tiempo (máximo 60 s)
            if config_time < 60:
                config_time += 1
                display.show(str(config_time))
        if button_b.was_pressed(): # Botón B: Decrementa el tiempo (mínimo 10 s)
            if config_time > 10:
                config_time -= 1
                display.show(str(config_time))
        if accelerometer.was_gesture('shake'): # Se detecta el gesto shake para armar la bomba
            current_state = STATE_COUNTDOWN
            countdown = config_time # Establece el tiempo de cuenta regresiva
            last_tick = utime.ticks_ms() # Registra el instante de inicio
            display.show("ARM")
            current_step = 0  # pa q empiece desde cero siempre la secuencia de desactivación de la bomba

    elif current_state == STATE_COUNTDOWN:
        now = utime.ticks_ms() 
        if utime.ticks_diff(now, last_tick) >= 1000: # Cada 1000 ms disminuye el contador
            countdown -= 1
            last_tick = now
            display.show(str(countdown))
        
        # Verificamos la secuencia de desactivación de la bomba, como ya había empezado en cero
        # el programa verifica que los pasos se den en el orden adecuado
        if button_a.was_pressed() and deactivation_sequence[current_step] == 'button_a':
            current_step += 1
        elif button_b.was_pressed() and deactivation_sequence[current_step] == 'button_b':
            current_step += 1
        elif accelerometer.was_gesture('shake') and deactivation_sequence[current_step] == 'shake':
            current_step += 1
        
        # Cancelación: Si se completa la ecuencia
        if current_step == len(deactivation_sequence):
            current_state = STATE_CONFIG
            continue  # Reinicia el bucle en modo configuración
        
        if countdown <= 0: # Cuando el tiempo llega a 0, se activa la explosión
            current_state = STATE_EXPLODED
            display.show(Image.SKULL)
            music.play(music.WAWAWAWAA)
            utime.sleep_ms(2000)
            display.scroll("BOOM")

    elif current_state == STATE_EXPLODED: 
        display.show(Image.SKULL)
        music.play(music.POWER_DOWN)
        if pin_logo.is_touched(): # Para reiniciar, se presiona el sensor touch
            current_state = STATE_CONFIG
            config_time = 20  # Se resetea el tiempo a su valor por defecto

```
