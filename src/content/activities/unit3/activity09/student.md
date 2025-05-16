## Solución a la novena actividad:
### Vectores de prueba:
ID | Estado inicial | Evento realizado | Fuente del evento | Resultado esperado | ¿Pasa el vector de prueba? SI[X]/NO[]
---|----------------|------------------|-------------------|--------------------|----------------------------
1 | CONFIG | Botón Izquierdo (Mouse) | p5.js | Tiempo countdown -1 (mínimo 10) | [X]
2 | CONFIG | Botón Derecho (Mouse) | p5.js | Tiempo countdown +1 (máximo 60) | [X]
3 | CONFIG | Agitar mouse (shake) | p5.js | Cambia a COUNTDOWN | [X]
4 | CONFIG | Botón A | micro:bit | Tiempo countdown -1 (mínimo 10) | [X]
5 | CONFIG | Botón B | micro:bit | Tiempo countdown +1 (máximo 60) | [X]
6 | CONFIG | Shake físico (micro:bit) | micro:bit | Cambia a COUNTDOWN | [X]
7 | COUNTDOWN | Esperar sin hacer nada | - | Cuenta atrás hasta 0 y explota | [X]
8 | COUNTDOWN | Secuencia correcta (r,l,r,s) Mouse | p5.js | Vuelve a CONFIG sin explotar | [X]
9 | COUNTDOWN | Secuencia incorrecta Mouse | p5.js | Sigue en COUNTDOWN, no desactiva | [X]
10 | COUNTDOWN | Secuencia correcta micro:bit (B,A,B,shake) | micro:bit | Vuelve a CONFIG sin explotar | [X]
11 | COUNTDOWN | Secuencia incorrecta micro:bit | micro:bit | Sigue en COUNTDOWN, no desactiva | [X]
12 | EXPLODED | Tocar logo (micro:bit) | micro:bit | Vuelve a CONFIG (reinicia) | [X]
13 | EXPLODED | Botón central mouse | p5.js | Vuelve a CONFIG (reinicia) | [X]
14 | Cualquier | Intentar bajar countdown debajo de 10 | ambas fuentes | Countdown no baja de 10 | [X]
15 | Cualquier | Intentar subir countdown arriba de 60 | ambas fuentes | Countdown no sube de 60 | [X]
16 | Cualquier | Shake durante EXPLODED | ambas fuentes | No tiene efecto (no cambia estado) | [X]
17 | Cualquier | Tocar logo micro:bit en CONFIG | micro:bit | No tiene efecto | [X]
18 | COUNTDOWN | Movimiento accidental leve (shake mouse) | p5.js | No genera shake accidental | [X]

### Pruebas de regresión:
ID | Descripción problema detectado | Causa del problema | Solución aplicada | Estado final
---|--------------------------------|--------------------|-------------------|-------------
R1 | Shake (micro:bit) no iniciaba countdown desde CONFIG | El evento serial "S" no estaba siendo escuchado correctamente en p5.js | Se añadió correctamente lectura de evento serial "S" desde micro:bit en p5.js |  Solucionado
R2 | Tras explosión, touch del micro:bit no reiniciaba | Evento serial "T" no se procesaba en estado EXPLODED | Añadí claramente manejo evento centerEvent en estado EXPLODED desde serial "T" |  Solucionado
R3 | No había feedback visual en el micro:bit al realizar un input (botones A, B, shake o touch) | No se había implementado visualización inmediata en micro:bit	| Implementé display.show() con caracteres específicos para cada evento recibido desde micro:bit (A, B, S, T)	| Solucionado
