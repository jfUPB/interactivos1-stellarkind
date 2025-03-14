## Solución a la sexta actividad:

### Vectores del Estado STATE_CONFIG:
- **Evento: _'A' (botón o serial)_:**  
    - **Acción esperada:** Incrementar tiempo (config_time) en 1 (máx. 60), mostrar en pantalla.
    - **Estado siguiente:** STATE_CONFIG
- **Evento: _'B' (botón o serial)_:**  
    - **Acción esperada:** Disminuir tiempo (config_time) en 1 (mín. 10), mostrar en pantalla.
    - **Estado siguiente:** STATE_CONFIG
- **Evento: _'S' (shake o serial)_:**  
    - **Acción esperada:** Armar bomba ("ARM"), inicializar cuenta regresiva (countdown).
    - **Estado siguiente:** STATE_COUNTDOWN
- **Evento no esperado: _'T' (touch o serial)_**  
    - **Acción esperada:** Ignorar el evento, continuar mostrando configuración.
    - **Estado siguiente:** STATE_CONFIG

### Vectores delEstado STATE_COUNTDOWN:  
- **Evento: _Esperar 1 segundo (sin eventos)_**  
    - **Acción esperada:** Disminuir contador (countdown) en 1, mostrar en pantalla.
    - **Estado siguiente:** STATE_COUNTDOWN
- **Evento: _Secuencia de desactivación correcta (A→B→A→S)_**  
    - **Acción esperada:** Al completar la secuencia correcta, volver a configuración.
    - **Estado siguiente:** STATE_CONFIG
- **Evento: _Secuencia incorrecta (ejemplo: A→A→B→S)_**
    - **Acción esperada:** Reiniciar secuencia, no desactivar, continuar cuenta regresiva.
    - **Estado siguiente:** STATE_COUNTDOWN
- **Evento no esperado: _'T' (touch o serial)_**
    - **Acción esperada:** No hacer nada, continuar con cuenta regresiva.
    - **Estado siguiente:** STATE_COUNTDOWN

- **Evento: _Countdown llega a 0 sin desactivar_**
    - **Acción esperada:** Mostrar calavera, reproducir sonido, mostrar "BOOM".
    - **Estado siguiente:** STATE_EXPLODED
  
### Vectores del Estado STATE_EXPLODED:
-  **Evento: _'T' (touch o serial)_**
    - **Acción esperada:** Reiniciar bomba, volver al estado configuración con tiempo inicial de 20.
    - **Estado siguiente:** STATE_CONFIG
- **Evento no esperado: _'A', 'B', 'S' (botones o serial)_**
    - **Acción esperada:** Ignorar eventos, permanecer en estado explotado.
    - **Estado siguiente:** STATE_EXPLODED
 
### Otros vectores de prueba:
- **Evento: _Enviar comando inválido por serial (ejemplo: "X")_**
    - **Acción esperada:** Encender LED indicador de error (posición (4,0)). Ignorar mensaje.
    - **Estado siguiente:** Estado actual se mantiene.
- **Evento: _Secuencia rápida y válida de eventos por puerto serial (A→B→A→S)_**
    - **Acción esperada:** Desactivar bomba correctamente si es ingresada en orden adecuado.
    - **Estado siguiente:** STATE_CONFIG
- **Evento: _Probar la combinación de entradas simultáneas (botones físicos+serial)_**
    - **Acción esperada:** Procesar eventos de forma ordenada y correcta según secuencia.
    - **Estado siguiente:** Según secuencia (correcta: STATE_CONFIG, incorrecta: STATE_COUNTDOWN)
