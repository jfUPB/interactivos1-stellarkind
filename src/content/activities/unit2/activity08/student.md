## Solución a la octava actividad:  
### - **Funcionamiento del Código**    
  - **Definición de la Clase Pixel:** Cada objeto de la clase Pixel representa un píxel en el display, ubicado en las coordenadas ```(pixelX, pixelY)```.  
    Al inicializarse, se le asigna un estado interno (almacenado en ```self.state```) que comienza en "Init", y se define un intervalo de tiempo ```(interval)``` que indica cada cuánto se debe actualizar el píxel.  
    ```self.pixelState``` guarda el nivel de brillo actual que se muestra en el display.  
  - **Método update():**  
      - **Estado "Init":** Al ingresar en este estado, el programa:  
          - Registra el tiempo actual (```self.startTime = utime.ticks_ms()```).  
          - Muestra el píxel en el display usando el estado inicial (```display.set_pixel(self.pixelX, self.pixelY, self.pixelState)```).  
          - Cambia su estado a ```WaitTimeout``` para pasar a la siguiente fase.  
      - **Estado ```WaitTimeout```:** En este estado, _el programa verifica continuamente si ha transcurrido el intervalo definido_, usando la función ```utime.ticks_diff(utime.ticks_ms(), self.startTime)```.  
          - **Evento de Timeout:** Cuando el tiempo transcurrido supera self.interval, se considera que se ha producido el evento.   
          - **Acción en Respuesta al Evento:** Se actualiza ```self.startTime``` para comenzar una nueva cuenta. Se alterna el valor de ```self.pixelState``` entre 0 y 9 (si es 9, cambia a 0; de lo contrario, pasa a 9).
            Se actualiza el display para reflejar el nuevo estado del píxel.  
      - **Creación y Ejecución:**  
          - Se instancian dos objetos ```Pixel (pixel1 y pixel2)``` con coordenadas y tiempos de intervalo distintos.  
          - En un bucle infinito, se llama al método ```update()``` de cada objeto, lo que permite que cada píxel funcione de forma independiente, mostrando su comportamiento de parpadeo o alternancia según su intervalo.  
### - **Identificación de Estados, Eventos y Acciones**  
  - **Estados:**  
      - **"Init":** Estado inicial en el que se configura el tiempo de inicio y se muestra el valor inicial del píxel. Este establece la configuración inicial y preparar el píxel para el ciclo de actualización.  
      - **"WaitTimeout":** Estado en el que el programa espera a que transcurra un intervalo de tiempo específico. Permite que el programa espere el momento adecuado para actualizar el estado del píxel.  
  - **Eventos:**   
      - **Timeout:** Ocurre cuando la diferencia entre el tiempo actual y self.startTime es mayor que ```self.interval```. Estyee sirve como disparador para actualizar el estado del píxel.  
  - **Acciones:**  
      - **En Estado "Init":**   
          - Registrar el tiempo actual (```self.startTime```).  
          - Mostrar el estado inicial del píxel en el display (```display.set_pixel(...)```).  
          - Transitar al estado ```WaitTimeout```.  
      - **En Estado "WaitTimeout":**  
          - Verificar si se cumplió el intervalo de tiempo.  
          - Al ocurrir el timeout:  
              - Actualizar el tiempo de inicio (```self.startTime```).  
              - Alternar el valor de ```self.pixelState``` (cambiando entre 0 y 9).  
              - Actualizar el display para reflejar el nuevo brillo del píxel.  
