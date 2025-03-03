## Solución a la duodécima actividad
### Código funcional de la bomba temporizada:
``` Python
from microbit import *
import utime
import music

# estados
STATE_CONFIG = 0       # Modo de configuración (bomba desarmada)
STATE_COUNTDOWN = 1    # Modo de cuenta regresiva (bomba armada)
STATE_EXPLODED = 2     # Estado de explosión

config_time = 20       # Tiempo inicial en segundos (valor configurable)
current_state = STATE_CONFIG

# Variables para la cuenta regresiva
countdown = config_time
last_tick = utime.ticks_ms()

while True:
    if current_state == STATE_CONFIG:
        # Se muestra el tiempo actual configurado.
        # Usamos display.scroll para informar al usuario ("Cfg: 20")
        display.scroll("Cfg: " + str(config_time))
        
        # Botón A: Incrementa el tiempo (máximo 60 s)
        if button_a.was_pressed():
            if config_time < 60:
                config_time += 1
                display.show(str(config_time))
                utime.sleep_ms(300)
        
        # Botón B: Decrementa el tiempo (mínimo 10 s)
        if button_b.was_pressed():
            if config_time > 10:
                config_time -= 1
                display.show(str(config_time))
                utime.sleep_ms(300)
        
        # Se detecta el gesto shake para armar la bomba
        if accelerometer.was_gesture('shake'):
            current_state = STATE_COUNTDOWN
            countdown = config_time          # Establece el tiempo de cuenta regresiva
            last_tick = utime.ticks_ms()       # Registra el instante de inicio
            display.show("ARM")
            utime.sleep_ms(500)
        
        utime.sleep_ms(100)
    
    elif current_state == STATE_COUNTDOWN:
        now = utime.ticks_ms()
        # Cada 1000 ms se decrementa el contador
        if utime.ticks_diff(now, last_tick) >= 1000:
            countdown -= 1
            last_tick = now
            display.show(str(countdown))
        
        # Cancelación: Si se toca el sensor touch (se asume que está conectado a pin0)
        if pin0.is_touched():
            current_state = STATE_CONFIG
            utime.sleep_ms(500)
            continue  # Reinicia el bucle en modo configuración
        
        # Cuando el tiempo llega a 0, se activa la explosión
        if countdown <= 0:
            current_state = STATE_EXPLODED
            display.show(Image.SKULL)
            display.scroll("BOOM")
            music.play(music.WAWAWAWAA)
        
        utime.sleep_ms(100)
    
    elif current_state == STATE_EXPLODED:
        # Se muestra un ícono de explosión y se puede mantener el sonido
        display.show(Image.SKULL)
        # Para reiniciar, se presiona el sensor touch (pin0)
        if pin0.is_touched():
            current_state = STATE_CONFIG
            config_time = 20  # Se resetea el tiempo a su valor por defecto
        
        utime.sleep_ms(100)
```
### Link a un video demostrando su funcionamiento básico
