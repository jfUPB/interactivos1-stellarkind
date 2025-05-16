## Solución a la novena actividad:
### - Código Semáforo con Máquina de Estados:
  ``` python
  from microbit import *
  import utime

  class TrafficLight:
    def __init__(self):
        self.state = "Red" #Empieza en rojo el semáforo 
        self.startTime = utime.ticks_ms()
        self.intervals = {
            "Red": 3000,    # 3 segundos en rojo
            "Green": 3000,  # 3 segundos en verde
            "Yellow": 1000  # 1 segundo en amarillo
        }
        self.update_display()

    def update_display(self):
        display.clear()
        # En la fila de leds de la mitad según el estado, enciende la luz que es
        if self.state == "Red":
            display.set_pixel(2, 0, 9)  # Luz roja en la parte superior
        elif self.state == "Yellow":
            display.set_pixel(2, 2, 9)  # Luz amarilla en el centro
        elif self.state == "Green":
            display.set_pixel(2, 4, 9)  # Luz verde en la parte inferior

    def update(self):
        current_time = utime.ticks_ms()
        # Se evalúa si ha transcurrido el intervalo del estado actual
        if utime.ticks_diff(current_time, self.startTime) > self.intervals[self.state]:
            # Reinicia el contador de tiempo
            self.startTime = current_time
            # Transición entre estados:
            if self.state == "Red":
                self.state = "Green"     # De rojo pasa a verde
            elif self.state == "Green":
                self.state = "Yellow"    # De verde pasa a amarillo
            elif self.state == "Yellow":
                self.state = "Red"       # De amarillo vuelve a rojo
            self.update_display()

  traffic_light = TrafficLight() # Objeto semáforo

  while True:
    traffic_light.update()
  ```
### - Estados, Eventos y Acciones:  
  - **Estados:**  
      - **"Red":** En este estado el semáforo muestra la luz roja (se enciende el LED en la parte superior central de la pantalla).  
      - **"Green":** En este estado se muestra la luz verde (se enciende el LED en la parte inferior de la pantalla).  
      - **"Yellow":** En este estado se muestra la luz amarilla (se enciende el LED central).  
  - **Eventos Evaluados:**  
      - **Evento de Timeout:** En cada estado, el programa evalúa continuamente si el tiempo transcurrido desde que se ingresó al estado (almacenado en ```self.startTime```) supera el intervalo 
        definido para ese estado (por ejemplo, 3000 ms para "Red" o "Green", y 1000 ms para "Yellow"). Este evento de timeout es el único evento que determina cuándo debe ocurrir la transición de un estado a otro.  
  -  **Acciones:**  
      - **Acciones en el Estado Inicial ("Red"):**  
          - Se registra el tiempo de inicio (```self.startTime = utime.ticks_ms()```).  
          - Se muestra la luz roja en el display mediante ```display.set_pixel(2, 0, 9)```.  
      - **Acciones al Ocurrir el Evento de Timeout:**  
          - Reinicio del contador tiermpo, Se actualiza self.startTime con el tiempo actual para iniciar el conteo del siguiente intervalo.  
          - Transición de Estado:
              -  Si el estado actual es "Red", se transita a "Green".
              -  Si es "Green", se transita a "Yellow".
              -  Si es "Yellow", se retorna a "Red".
      - Se llama a ```update_display()``` para encender el LED correspondiente al nuevo estado.
