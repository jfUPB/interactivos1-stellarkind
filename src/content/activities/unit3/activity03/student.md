## Solución a la tercer actividad
### **El programa se divide en dos tareas principales:**
  - _**Eventos():**_ Lee los botones, el gesto shake, el sensor touch y también el puerto serial. Cuando se detecta alguna entrada, se marca que ocurrió un evento y se guarda el valor (por ejemplo, 'A' para botón A, 'B' 
para botón B, 'S' para shake, y 'T' para touch).  
  - _**Bomba():**_ Controla el comportamiento de la bomba según su estado (configuración, cuenta regresiva o explosión). En el estado de configuración, se pueden ajustar los tiempos o armar la bomba con un shake. En el 
estado de cuenta regresiva, se va decrementando el tiempo cada segundo y se revisa si se ingresa la secuencia correcta de desactivación (A, B, A, shake). Si la secuencia se completa, la bomba se desactiva y 
vuelve a configuración. Si el tiempo llega a cero, la bomba explota y muestra un ícono y el mensaje "BOOM". Para reiniciar desde la explosión, se debe usar el sensor touch (o mensaje 'T').  
### **Estados de la bomba:**
  - _**Estado de Configuración (STATE_CONFIG):**_ En este estado, la bomba está desarmada. Aquí se muestra en el display el tiempo configurado (por ejemplo, 20 segundos) y el usuario puede modificar este tiempo
    usando los botones A (para aumentar) y B (para disminuir). Además, si se detecta un gesto de shake o se recibe un mensaje "S" por el puerto serial, se arma la bomba y se pasa al siguiente estado. Es como la
    etapa de “preparación”, donde se decide cuánto tiempo tendrá la cuenta regresiva.
  - _**Estado de Cuenta Regresiva (STATE_COUNTDOWN):**_ Una vez armada, la bomba entra en este estado. Aquí el display muestra el tiempo restante, que se va reduciendo cada segundo. Además, durante este tiempo se
    escucha si el usuario ingresa la secuencia de desactivación correcta (A, B, A, shake) ya sea mediante los sensores o el puerto serial. Si la secuencia se ingresa correctamente, la bomba se desactiva y vuelve
    al estado de configuración. Si no se ingresa la secuencia y el tiempo llega a cero, la bomba pasa al estado de explosión.
  - _**Estado de Explosión (STATE_EXPLODED):**_ Este es el estado final cuando la cuenta regresiva llega a 0 y la bomba "explota". En este estado, el micro:bit muestra un ícono de explosión (por ejemplo, una calavera)
    y reproduce un sonido fuerte. La bomba permanece en este estado hasta que se detecta un evento de reinicio (por ejemplo, el sensor touch o el mensaje 'T' por el puerto serial), momento en el cual se reinicia
     el sistema y vuelve al estado de configuración.
### Código del programa: 
``` Python
from microbit import *
import utime
import music
uart.init(baudrate=115200) #Para inicializar el puerto serial

# estados
STATE_CONFIG = 0       # Modo de configuración (bomba desarmada)
STATE_COUNTDOWN = 1    # Modo de cuenta regresiva (bomba armada)
STATE_EXPLODED = 2     # Estado de explosión

config_time = 20  #Valor que es configurable
current_state = STATE_CONFIG
#para la cuenta regresiva
countdown = config_time
last_tick = utime.ticks_ms()

evento_ocurrio=False
valor_evento= ''

# Secuencia de desactivación y contador de pasos
deactivation_sequence = ['A', 'B', 'A', 'S']
paso_des_actual = 0

def Eventos():
    global evento_ocurrio,valor_evento
    if button_a.was_pressed():
        evento_ocurrio=True
        valor_evento='A'
    elif button_b.was_pressed():
        evento_ocurrio=True
        valor_evento='B'
    elif accelerometer.was_gesture('shake'):
        evento_ocurrio=True
        valor_evento='S'
    elif pin_logo.is_touched():
        evento_ocurrio=True
        valor_evento='T'
    if uart.any():
        dato=uart.read(1)
        if dato:
            try:
                ch=dato.decode('utf-8')
                if ch in ['A','B','S','T']:
                    evento_ocurrio=True
                    valor_evento=ch
                else:
                    display.set_pixel(4,0,9)
            except:
                pass

def Bomba():
    global current_state, config_time,countdown,last_tick,evento_ocurrio,valor_evento,paso_des_actual
    if current_state==STATE_CONFIG:
        display.scroll(str(config_time))
        if evento_ocurrio:
            if valor_evento=='A':
                if config_time < 60:
                    config_time += 1
                    display.show(str(config_time))
            elif valor_evento=='B':
                if config_time > 10:
                    config_time -= 1
                    display.show(str(config_time))
            elif valor_evento=='S':
                    current_state = STATE_COUNTDOWN
                    countdown = config_time # Establece el tiempo de cuenta regresiva
                    last_tick = utime.ticks_ms() # Registra el instante de inicio
                    display.show("ARM")
                    paso_des_actual=0
            
            evento_ocurrio=False
            valor_evento=''

    elif current_state == STATE_COUNTDOWN:

        now = utime.ticks_ms() 
        if utime.ticks_diff(now, last_tick) >= 1000: # Cada 1000 ms disminuye el contador
            countdown -= 1
            last_tick = now
            display.show(str(countdown))
        
        # Verificamos la secuencia de desactivación de la bomba, como ya había empezado en cero
        # el programa verifica que los pasos se den en el orden adecuado
        if evento_ocurrio:
            evento_ocurrio=False
            if valor_evento==deactivation_sequence[paso_des_actual]:
                paso_des_actual += 1
                if paso_des_actual ==len(deactivation_sequence):
                    current_state=STATE_CONFIG
                    return
            else: #en caso que se haga mal la secuencia de desactivación
                paso_des_actual=0

        
        if countdown <= 0: # Cuando el tiempo llega a 0, se activa la explosión
            current_state = STATE_EXPLODED
            display.show(Image.SKULL)
            music.play(music.WAWAWAWAA)
            utime.sleep_ms(2000)
            display.scroll("BOOM")

    elif current_state == STATE_EXPLODED: 
        display.show(Image.SKULL)
        music.play(music.POWER_DOWN)
        
        if evento_ocurrio: # Para reiniciar, se presiona el sensor touch
            if valor_evento=='T':
                current_state = STATE_CONFIG
                config_time = 20  # Se resetea el tiempo a su valor por defecto
            
            evento_ocurrio=False
            valor_evento=''
    
    
while True:
    Bomba()
    Eventos()
```
