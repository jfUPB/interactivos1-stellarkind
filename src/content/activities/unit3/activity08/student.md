## Solución a la octava actividad
### Control remoto desde el micro:bit
**Código Actualizado del Micro:bit (Python): **
``` python

from microbit import *
import utime
import music

# Inicializar UART para comunicación serial
uart.init(baudrate=115200)

# Estados
STATE_CONFIG = 0       # Modo de configuración (bomba desarmada)
STATE_COUNTDOWN = 1    # Modo de cuenta regresiva (bomba armada)
STATE_EXPLODED = 2     # Estado de explosión

# Variables de estado y configuración
config_time = 20       # Valor configurable inicial
current_state = STATE_CONFIG
countdown = config_time
last_tick = utime.ticks_ms()

# Variables para manejo de eventos (locales y remotos)
evento_local_ocurrio = False
valor_evento = ''

# Secuencia de desactivación y contador de pasos
# IMPORTANTE: Asegúrate que coincida con la de p5.js ('A', 'B', 'A', 'S')
deactivation_sequence = ['A', 'B', 'A', 'S']
paso_des_actual = 0

# --- Funciones de Comunicación ---
def send_state_update():
    """Envía el estado actual y el tiempo relevante a p5.js"""
    if current_state == STATE_CONFIG:
        uart.write("C" + str(config_time) + "\n")
    elif current_state == STATE_COUNTDOWN:
        # El mensaje 'ARM' se envía al cambiar de estado
        # Se enviarán mensajes 'T' en el bucle principal
        pass # No necesita enviar estado constantemente, solo al inicio y Ticks
    elif current_state == STATE_EXPLODED:
        # El mensaje 'BOOM' se envía al cambiar de estado
        pass # No necesita enviar estado constantemente

def process_serial_input():
    """Procesa los comandos recibidos desde p5.js vía UART"""
    global evento_local_ocurrio, valor_evento
    if uart.any():
        data_bytes = uart.read()
        if data_bytes:
            try:
                # Decodifica y elimina espacios/saltos de línea
                command = data_bytes.decode('utf-8').strip()
                if command in ['A', 'B', 'S', 'T']:
                    # Trata el comando recibido como un evento local
                    valor_evento = command
                    # ¡Importante! Procesar este evento inmediatamente en la lógica de Bomba()
                    # llamando a la función o manejándolo en el siguiente ciclo.
                    # Para simplificar, llamaremos directamente a la lógica de manejo de eventos.
                    handle_event(valor_evento)
                elif command == "SYNC": # Comando para que p5.js pida el estado actual
                     send_state_update() # Envia el estado actual como respuesta
                else:
                    # Ignorar otros comandos o mostrar error si es necesario
                    # display.set_pixel(4, 0, 9) # Indicador de comando desconocido
                    # sleep(500)
                    # display.set_pixel(4, 0, 0)
                    pass
            except Exception as e:
                # Error al decodificar o procesar
                # print("Serial Error:", e) # Descomentar para depuración
                pass

# --- Funciones de Lógica de la Bomba ---

def handle_event(event_code):
    """Maneja un evento ('A', 'B', 'S', 'T') según el estado actual."""
    global current_state, config_time, countdown, last_tick, paso_des_actual

    if current_state == STATE_CONFIG:
        if event_code == 'A':
            if config_time < 60:
                config_time += 5 # Incremento más rápido para prueba
                display.scroll(str(config_time), wait=False, loop=True)
                uart.write("C" + str(config_time) + "\n") # Enviar actualización
        elif event_code == 'B':
            if config_time > 10:
                config_time -= 5 # Decremento más rápido para prueba
                display.scroll(str(config_time), wait=False, loop=True)
                uart.write("C" + str(config_time) + "\n") # Enviar actualización
        elif event_code == 'S': # 'Shake' para armar
            current_state = STATE_COUNTDOWN
            countdown = config_time
            last_tick = utime.ticks_ms()
            paso_des_actual = 0
            display.show(Image.TARGET) # Indicador visual de armado
            music.play(music.POWER_UP, wait=False)
            uart.write("ARM\n") # Notificar a p5.js
            sleep(500) # Pequeña pausa para mostrar imagen
            display.show(str(countdown))
            uart.write("T" + str(countdown) + "\n") # Enviar tiempo inicial

    elif current_state == STATE_COUNTDOWN:
        # Procesar secuencia de desactivación
        if event_code == deactivation_sequence[paso_des_actual]:
            paso_des_actual += 1
            music.play(music.BA_DING, wait=False) # Sonido de paso correcto
            # Muestra progreso (opcional)
            display.set_pixel(paso_des_actual -1 , 4, 9)

            if paso_des_actual == len(deactivation_sequence):
                current_state = STATE_CONFIG
                display.show(Image.YES) # Desactivada
                music.play(music.ENTERTAINER, wait=False)
                uart.write("DISARM\n") # Notificar a p5.js
                sleep(1000)
                display.scroll(str(config_time), wait=False, loop=True) # Volver a mostrar tiempo config
                uart.write("C" + str(config_time) + "\n")
                return # Salir para evitar procesamiento adicional en este ciclo
        elif event_code in ['A', 'B', 'S', 'T']: # Si se presiona algo incorrecto en la secuencia
            paso_des_actual = 0 # Reiniciar secuencia
            music.play(music.WAWAWAWAA, wait=False) # Sonido de error
            # Limpiar pixeles de progreso (opcional)
            for i in range(len(deactivation_sequence)):
                display.set_pixel(i, 4, 0)


    elif current_state == STATE_EXPLODED:
        if event_code == 'T': # 'Touch' para reiniciar
            current_state = STATE_CONFIG
            config_time = 20
            countdown = config_time # Resetear countdown también
            display.scroll(str(config_time), wait=False, loop=True)
            music.play(music.JUMP_UP, wait=False)
            uart.write("RESET\n") # Notificar a p5.js
            uart.write("C" + str(config_time) + "\n") # Enviar nuevo tiempo config

# --- Bucle Principal ---
display.scroll(str(config_time), wait=False, loop=True) # Mostrar config inicial
uart.write("C" + str(config_time) + "\n") # Enviar config inicial a p5

while True:
    # 1. Procesar Entradas Locales (Botones, Accel, Touch)
    evento_local_ocurrio = False
    local_event_val = ''
    if button_a.was_pressed():
        evento_local_ocurrio = True
        local_event_val = 'A'
        uart.write("A\n") # Notificar a p5.js del evento local
    elif button_b.was_pressed():
        evento_local_ocurrio = True
        local_event_val = 'B'
        uart.write("B\n") # Notificar a p5.js
    elif accelerometer.was_gesture('shake'):
        evento_local_ocurrio = True
        local_event_val = 'S'
        uart.write("S\n") # Notificar a p5.js
    elif pin_logo.is_touched():
        evento_local_ocurrio = True
        local_event_val = 'T'
        uart.write("T\n") # Notificar a p5.js

    # Si hubo un evento local, manejarlo
    if evento_local_ocurrio:
        handle_event(local_event_val)

    # 2. Procesar Entradas Remotas (Serial desde p5.js)
    process_serial_input() # Lee y procesa comandos como 'A', 'B', 'S', 'T'

    # 3. Lógica de Estados (Actualización del Temporizador, Transiciones)
    if current_state == STATE_COUNTDOWN:
        now = utime.ticks_ms()
        if utime.ticks_diff(now, last_tick) >= 1000: # Cada segundo
            countdown -= 1
            last_tick = now
            if countdown >= 0:
                 display.show(str(countdown))
                 uart.write("T" + str(countdown) + "\n") # Enviar actualización a p5.js
                 # Añadir pitido corto cada segundo
                 if countdown <= 5 and countdown > 0:
                     music.pitch(2000 - countdown * 200, 50) # Pitido más agudo al final
                 elif countdown > 0:
                     music.pitch(440, 20) # Pitido normal

            if countdown <= 0: # ¡Explosión!
                current_state = STATE_EXPLODED
                display.show(Image.SKULL)
                music.play(music.WAWAWAWAA, wait=True) # Sonido largo
                uart.write("BOOM\n") # Notificar a p5.js
                sleep(1000)
                display.scroll("BOOM", wait=False, loop=True)


    elif current_state == STATE_EXPLODED:
        # La animación de EXPLODED se maneja dentro de handle_event al detectar 'T'
        # Podríamos añadir una animación continua aquí si quisiéramos
        if utime.ticks_ms() % 1000 < 500:
             display.show(Image.SKULL)
        else:
             display.clear()
        # El manejo del reinicio por 'T' ya está en handle_event

    # Pequeña pausa para no sobrecargar el procesador
    sleep(20)
```

**Código Actualizado de p5.js (JavaScript):**
``` js
let serial; // Variable para el objeto Serial Port
let portName = '/dev/tty.usbmodem14101'; // ****** cambiar por el puerto serial ******

let currentSerialData = ""; // Para acumular datos seriales entrantes

let displayCountdown = 20; // Valor que se muestra en pantalla (actualizado por Micro:bit)
let configTimeDisplay = 20; // Valor de configuración mostrado (actualizado por Micro:bit)
let state = "CONFIG"; // Estado actual (actualizado por Micro:bit)
let lastReceivedState = ""; // Para evitar procesamiento redundante

// Variables para la interfaz y eventos locales (ya no controlan el estado directamente)
let shakeCounter = 0;
const shakeThreshold = 15; // Ajusta la sensibilidad del "shake"
let lastMouseX, lastMouseY;
let cooldownActive = false; // Para evitar envíos múltiples de shake

// Secuencia de desactivación VISUAL (no funcional, solo para referencia si se quiere mostrar)
// La secuencia REAL la valida el Micro:bit
let deactivation_sequence_ref = ["A", "B", "A", "S"];
let entered_sequence = []; // Para mostrar lo que el usuario introduce

function setup() {
  createCanvas(400, 400);
  textAlign(CENTER, CENTER);
  textSize(40);
  lastMouseX = mouseX;
  lastMouseY = mouseY;

  // Configuración del puerto serial
  serial = new p5.SerialPort();    // Crea el objeto
  serial.list();                   // Lista los puertos disponibles (en la consola del navegador)
  serial.on('list', gotList);      // Callback cuando la lista está lista
  serial.on('data', gotData);      // Callback cuando llegan datos
  serial.on('error', gotError);    // Callback para errores
  serial.on('open', gotOpen);      // Callback cuando el puerto se abre
  serial.on('close', gotClose);    // Callback cuando el puerto se cierra

  // Intenta abrir el puerto especificado en portName
  // Asegúrate que la app p5.serialcontrol esté corriendo
  serial.openPort(portName);

  // Solicitar estado inicial al Micro:bit después de un breve retraso
  // para asegurar que la conexión esté establecida.
  setTimeout(() => {
     if (serial.isOpen()) {
       console.log("Requesting initial state from Micro:bit...");
       serial.write("SYNC\n"); // Pide al Micro:bit que envíe su estado actual
     } else {
       console.log("Serial port not open yet for initial sync.");
     }
  }, 2000); // Espera 2 segundos
}

// --- Callbacks de p5.serialport ---

function gotList(portList) {
  // portList contiene un array de nombres de puertos disponibles
  console.log("Available Serial Ports:");
  for (let i = 0; i < portList.length; i++) {
    console.log(portList[i]);
  }
  // Podrías añadir lógica aquí para seleccionar un puerto de la lista
}

function gotOpen() {
  console.log("Serial Port Opened: " + portName);
}

function gotClose() {
  console.log("Serial Port Closed");
}

function gotError(theerror) {
  console.error("Serial Port Error: " + theerror);
  // Podrías intentar reabrir el puerto aquí si es necesario
}

function gotData() {
  let currentString = serial.readStringUntil('\n'); // Lee hasta encontrar un salto de línea
  if (currentString === null || currentString.length <= 0) return; // Si no hay nada, sal

  currentString = currentString.trim(); // Limpia espacios en blanco
  console.log("Received:", currentString); // Muestra en consola lo recibido

  if (currentString === lastReceivedState && !currentString.startsWith("T")) {
      // Evita procesar el mismo mensaje de estado repetidamente, excepto los Ticks 'T'
     // return;
  }
   lastReceivedState = currentString;


  // --- Procesar Mensajes del Micro:bit ---
  let command = currentString.charAt(0);
  let value = currentString.substring(1); // El resto de la cadena

  switch (command) {
    case 'C': // Config Time Update
      let newConfigTime = parseInt(value);
      if (!isNaN(newConfigTime)) {
        configTimeDisplay = newConfigTime;
        if (state === "CONFIG") {
             displayCountdown = configTimeDisplay; // Actualiza el display si estamos en config
        }
        console.log("Updated Config Time: " + configTimeDisplay);
      }
      state = "CONFIG"; // Asegura que estamos en CONFIG
      entered_sequence = []; // Limpia secuencia entrada
      break;

    case 'A': // Armed
       if (currentString === "ARM") { // Asegura que es el comando completo
            state = "COUNTDOWN";
            console.log("State changed to COUNTDOWN");
            entered_sequence = []; // Limpia secuencia entrada al armar
       }
      break;

    case 'T': // Countdown Tick
      let newCountdown = parseInt(value);
      if (!isNaN(newCountdown)) {
        displayCountdown = newCountdown;
         if (state !== "COUNTDOWN") {
             state = "COUNTDOWN"; // Asegura que estamos en COUNTDOWN si recibimos ticks
             console.log("Forcing state to COUNTDOWN due to Tick");
         }
        console.log("Countdown Tick: " + displayCountdown);
      }
      break;

    case 'D': // Disarmed
        if (currentString === "DISARM") { // Asegura que es el comando completo
            state = "CONFIG";
            displayCountdown = configTimeDisplay; // Muestra el tiempo de config de nuevo
            console.log("State changed to CONFIG (Disarmed)");
            entered_sequence = [];
        }
      break;

    case 'B': // Boom!
        if (currentString === "BOOM") { // Asegura que es el comando completo
            state = "EXPLODED";
            console.log("State changed to EXPLODED");
            entered_sequence = [];
        }
      break;

    case 'R': // Reset
        if (currentString === "RESET") { // Asegura que es el comando completo
            state = "CONFIG";
            // El Micro:bit enviará un 'C' con el nuevo tiempo, así que no lo forzamos aquí
            // displayCountdown = 20; // Opcional: poner valor por defecto mientras llega el 'C'
            console.log("State changed to CONFIG (Reset)");
            entered_sequence = [];
        }
      break;

    // Podríamos añadir casos para 'E<evento>' si quisiéramos feedback visual de eventos
    default:
      console.log("Unknown command received: " + currentString);
      break;
  }
}

// --- Función Draw (Visualización) ---

function draw() {
  background(50); // Fondo oscuro

  // Dibuja el indicador de estado visual (similar al original)
  fill(100);
  rect(width / 2 - 75, height / 2 + 60, 150, 50, 10); // Caja base
  let indicatorColor;

  // Dibuja el texto principal (Tiempo o Estado)
  fill(255); // Texto blanco por defecto
  textSize(80); // Tamaño grande para el número/estado

  switch (state) {
    case "CONFIG":
      indicatorColor = color(0, 255, 0); // Verde
      textSize(40);
      text("CONFIG MODE", width / 2, 80);
      textSize(80);
      text(configTimeDisplay, width / 2, height / 2 - 20); // Muestra tiempo configurable
      drawSequenceInput(); // Muestra la secuencia que se está introduciendo (vacía en config)
      break;

    case "COUNTDOWN":
      indicatorColor = color(255, 255, 0); // Amarillo
      textSize(40);
      fill(255, 165, 0); // Naranja para "ARMED"
      text("ARMED!", width / 2, 80);
      fill(255); // Blanco para el contador
      textSize(100); // Más grande en cuenta regresiva
      text(displayCountdown, width / 2, height / 2 - 20);
      drawSequenceInput(); // Muestra la secuencia que se está introduciendo
      break;

    case "EXPLODED":
      indicatorColor = color(255, 0, 0); // Rojo
       // Fondo parpadeante
       if (frameCount % 20 < 10) {
          background(255, 0, 0); // Rojo
       } else {
          background(0); // Negro
       }
      textAlign(CENTER, CENTER);
      textSize(100);
      if (frameCount % 20 < 10) {
         fill(0); // Texto Negro si el fondo es rojo
      } else {
         fill(255); // Texto Blanco si el fondo es negro
      }
       text("💀", width / 2, height / 2 - 50); // Calavera
       textSize(60);
       text("BOOM!", width / 2, height / 2 + 40);
       drawSequenceInput(); // Muestra la secuencia (vacía en exploded)
      break;

     default: // Estado desconocido o inicial
       indicatorColor = color(128); // Gris
       text("Connecting...", width / 2, height / 2);
       break;
  }

  // Dibuja el LED indicador de estado
  noStroke();
  fill(indicatorColor);
  ellipse(width / 2, height / 2 + 85, 30, 30); // LED

   // Detectar shake del mouse (solo si no está en cooldown)
   detectShake();
}

function drawSequenceInput() {
    // Muestra la secuencia que el usuario ha introducido en p5.js
    fill(200);
    textSize(20);
    let seqStr = entered_sequence.join(' ');
    text("Input: " + seqStr, width / 2, height - 30);
}


// --- Funciones de Eventos de Usuario (p5.js) ---

function mousePressed() {
   if (!serial.isOpen()) {
     console.log("Cannot send event: Serial port not open.");
     return; // No hacer nada si el puerto no está abierto
   }

  let eventToSend = null;

  if (mouseButton === LEFT) {
    console.log("LOCAL EVENT: LEFT CLICK (Sending 'A')");
    eventToSend = 'A';
    if (state === "COUNTDOWN") entered_sequence.push('A'); // Añadir a la secuencia visual
  } else if (mouseButton === RIGHT) {
    console.log("LOCAL EVENT: RIGHT CLICK (Sending 'B')");
    eventToSend = 'B';
     if (state === "COUNTDOWN") entered_sequence.push('B'); // Añadir a la secuencia visual
  } else if (mouseButton === CENTER) {
    // El clic central ahora siempre envía 'T' (Touch)
    console.log("LOCAL EVENT: CENTER CLICK (Sending 'T')");
    eventToSend = 'T';
     // No se añade 'T' a la secuencia de desactivación visualmente
     // (aunque podrías hacerlo si la secuencia lo incluyera)
  }

  if (eventToSend) {
    serial.write(eventToSend + '\n'); // Envía el evento al Micro:bit
  }

  // Limitar la longitud de la secuencia visual mostrada
   if (entered_sequence.length > deactivation_sequence_ref.length) {
       entered_sequence.shift(); // Elimina el más antiguo si se excede
   }
}

function detectShake() {
    if (cooldownActive || !serial.isOpen()) return; // Salir si hay cooldown o puerto cerrado

    let movement = dist(mouseX, mouseY, lastMouseX, lastMouseY);
    if (movement > 30) { // Umbral de movimiento para contar como parte de un shake
        shakeCounter++;
        //console.log("Shake count:", shakeCounter); // Para depuración
        if (shakeCounter >= shakeThreshold) {
            console.log("LOCAL EVENT: MOUSE SHAKE (Sending 'S')");
            serial.write('S\n'); // Envía 'S' al Micro:bit
             if (state === "COUNTDOWN") entered_sequence.push('S'); // Añadir a la secuencia visual
            shakeCounter = 0; // Reinicia el contador de shake
            cooldownActive = true; // Activa cooldown para evitar envíos múltiples
            setTimeout(() => { cooldownActive = false; }, 1500); // Desactiva cooldown después de 1.5 seg
        }
    } else {
        // Si no hay movimiento significativo, decrementa el contador gradualmente
        if (shakeCounter > 0) {
           // shakeCounter--;
        }
    }

    lastMouseX = mouseX;
    lastMouseY = mouseY;

    // Resetear contador si no hay movimiento por un tiempo podría ser útil también
    // (implementación más compleja)
}

// Opcional: para limpiar el contador de shake si el mouse se detiene completamente
function mouseMoved() {
    // Podríamos resetear shakeCounter aquí si el mouse se mueve *lentamente*
    // Pero la lógica actual en detectShake es probablemente suficiente.
}
```
