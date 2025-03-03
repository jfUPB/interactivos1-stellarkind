## Solución a la decimocuarta actividad  
### Vectores de prueba:  
### **1)** Configuración del Tiempo:  
- **Objetivo:** Verificar que el tiempo de la bomba se puede ajustar correctamente usando los botones UP y DOWN.  
- **Procedimiento:**  
  - Iniciar en el estado de configuración.   
  - Presionar el botón lentamente UP cinco veces.    
  - Presionar el botón lentamente DOWN tres veces.  
- **Resultado Esperado:** El tiempo debería incrementarse a 25 segundos después de presionar UP y luego disminuir a 22 segundos después de presionar DOWN.   
- **Resultado Obtenido:** El tiempo se incrementó y disminuyó correctamente.   
- **Errores Encontrados:** Ninguno en este vector de prueba.  

### **2)** Transición a Cuenta Regresiva:  
- **Objetivo:** Comprobar la transición del estado de configuración al estado de cuenta regresiva mediante el gesto de shake.  
- **Procedimiento:**  
  - Configurar el tiempo a 20 segundos.  
  - Simular un gesto de shake para armar la bomba.  
- **Resultado Esperado:** La bomba debería pasar al estado de cuenta regresiva y comenzar a decrementar desde 20 segundos.  
- **Resultado Obtenido:** La bomba armada y comenzó el conteo regresivo correctamente.   
- **Errores Encontrados:** Ninguno en este vector de prueba.  

### **3)** Interrupción y Reinicio:  
- **Objetivo:** Evaluar la capacidad de interrumpir la cuenta regresiva y reiniciar la configuración.  
- **Procedimiento:**  
  - Iniciar la cuenta regresiva.  
  - Interferir tocando el botón touch durante la cuenta regresiva.  
- **Resultado Esperado:** La cuenta regresiva debería detenerse y el sistema debería volver al estado de configuración.  
- **Resultado Obtenido:** Al tocar el botón touch, la bomba no regresó a la configuración inicial.  
- **Errores Encontrados:** La bomba no respondió al toque durante la cuenta regresiva.  
- **Corrección:** Implementación de la detección correcta del toque en el estado de cuenta regresiva.

### **4)** Responsividad en la Configuración del Tiempo:  
- **Objetivo:** Evaluar la rapidez y fluidez con la que se actualiza el tiempo en el display cuando se modifican los valores de tiempo con los botones UP y DOWN.  
- **Procedimiento:**  
  - Iniciar en el estado de configuración.  
  - Presionar el botón UP cinco veces y el botón DOWN tres veces.  
- **Resultado Esperado:**  El tiempo debería ajustarse rápidamente sin demora perceptible, mostrando cada nuevo valor en el display inmediatamente después de cada presión de botón.  
- **Resultado Obtenido:** Hubo una demora notable en la actualización del display, especialmente al utilizar display.scroll("Cfg: " + str(config_time)), lo que hacía que la visualización del tiempo se retrasara detrás de las pulsaciones del botón.  
- **Errores Encontrados:** La demora fue causada por el uso de display.scroll con una cadena larga y múltiples llamadas a utime.sleep_ms().  
- **Corrección:** Simplificar el mensaje mostrado en el display a solo el tiempo (display.scroll(str(config_time))) y eliminar las llamadas a utime.sleep_ms() para mejorar la respuesta del sistema.  

### **5)** Comportamiento al Finalizar la Cuenta Regresiva  
- **Objetivo:** Verificar que el sistema transita correctamente al estado STATE_EXPLODED cuando la cuenta regresiva llega a cero, mostrando el ícono adecuado, reproduciendo el sonido y luego mostrando el mensaje "BOOM".  
- **Procedimiento:**  
  - Configurar el tiempo inicial a un valor bajo para la prueba, como 3 segundos, para observar rápidamente el comportamiento al llegar a cero.  
  - Permitir que el sistema inicie la cuenta regresiva y llegue a cero.  
- **Resultado Esperado:**  
  - El sistema debería mostrar el ícono Image.SKULL en la pantalla del micro:bit cuando la cuenta regresiva alcance cero.  
  - Inmediatamente después de mostrar el ícono, el sistema debería reproducir el sonido music.WAWAWAWAA.  
  - Tras una pausa de 2000 milisegundos con el ícono visible, el sistema debería desplazar el mensaje "BOOM" en el display.  
- **Pasos Detallados y Resultados Esperados en Cada Paso:**  
  - Paso 1: Luego de armar la bomba, en el momento que el contador muestra 0, verificar que:  
      - Se muestra el ícono Image.SKULL.  
      - Se reproduce el sonido music.WAWAWAWAA.  
  - Paso 2: Confirmar que después de los 2000 milisegundos, el display comienza a desplazar el mensaje "BOOM", moviéndose a través de la pantalla.  
- **Resultados Obtenidos:** No se ve la calavera antes de que aparezca BOOM con el sonido pero si se muestra el BOOM y el sonido durante el tiempo esperado.  
- **Errores Encontrados:** El ícono Image.SKULL no se muestra correctamente.
- **Corrección:** Agregamos un corto tiempo de espera luego de display.show(Image.SKULL) y music.play(music.WAWAWAWAA), para que este no se interrumpa prematuramente y se evidencie de forma correcta.

### Código final corregido:
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
        # Usamos display.scroll para informar al usuario ("20")
        display.scroll(str(config_time))

        # Botón A: Incrementa el tiempo (máximo 60 s)
        if button_a.was_pressed():
            if config_time < 60:
                config_time += 1
                display.show(str(config_time))

        # Botón B: Decrementa el tiempo (mínimo 10 s)
        if button_b.was_pressed():
            if config_time > 10:
                config_time -= 1
                display.show(str(config_time))

        # Se detecta el gesto shake para armar la bomba
        if accelerometer.was_gesture('shake'):
            current_state = STATE_COUNTDOWN
            countdown = config_time          # Establece el tiempo de cuenta regresiva
            last_tick = utime.ticks_ms()       # Registra el instante de inicio
            display.show("ARM")

        utime.sleep_ms(100)

    elif current_state == STATE_COUNTDOWN:
        now = utime.ticks_ms()
        # Cada 1000 ms se decrementa el contador
        if utime.ticks_diff(now, last_tick) >= 1000:
            countdown -= 1
            last_tick = now
            display.show(str(countdown))

        # Cancelación: Si se toca el sensor touch
        if pin_logo.is_touched():
            current_state = STATE_CONFIG
            continue  # Reinicia el bucle en modo configuración

        # Cuando el tiempo llega a 0, se activa la explosión
        if countdown <= 0:
            current_state = STATE_EXPLODED
            display.show(Image.SKULL)
            music.play(music.WAWAWAWAA)
            utime.sleep_ms(2000)
            display.scroll("BOOM")

    elif current_state == STATE_EXPLODED:
        # Se muestra un ícono de explosión y se puede mantener el sonido
        display.show(Image.SKULL)
        music.play(music.POWER_DOWN)
        
        # Para reiniciar, se presiona el sensor touch
        if pin_logo.is_touched():
            current_state = STATE_CONFIG
            config_time = 20  # Se resetea el tiempo a su valor por defecto
```
