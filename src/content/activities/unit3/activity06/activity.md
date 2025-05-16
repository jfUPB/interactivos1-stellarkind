#### Vectores de prueba

Lee con detenimiento el enunciado y la entrega de la actividad antes de comenzar a trabajar. 
Notarás que deberás registrar algunos vectores de prueba fallidos y explicar por qué fallaron 
y cómo los corregiste.

**Enunciado**: usando el diagrama de estados que hiciste en la actividad anterior, 
crea la mayor cantidad de vectores de prueba para verificar que tu programa funciona correctamente.

Puedes crear los vectores así:

- Si estoy en el ESTADO_X y recibo el evento Y, entonces deben ocurrir estas acciones y 
debo ir al ESTADO_Z.

Trata de pararte en cada estado y pensar en todos los eventos que pueden ocurrir, tanto aquellos 
que modelaste como los que no modelaste. La idea es que detectes posibles errores en tu programa. 
Crear la mayor cantidad de pruebas. Entre más pruebas crees, más seguro estarás de que tu programa
funciona correctamente.

Una vez que crees los vectores de prueba, ejecuta tu programa y verifica que todos los vectores
de prueba pasen. Si alguno no pasa, entonces debes explicar por qué no pasó y modificar tu programa
para que pase. Ten presente que tendrás que volver a verificar todos los vectores de prueba desde el
principio. Este proceso se llama **pruebas de regresión** y es muy importante en el desarrollo de software.

No olvides que debes verificar la generación de eventos por parte de los sensores del micro:bit y
por parte del puerto serial.

**Entrega**: 

La primera vez que pruebes tu programa:

- Una lista con todos los vectores de prueba que creaste. Puedes marcar los que pasaron así: [X] y 
los que no así [ ].

Recuerda que si algún vector de prueba falla, deberás explicar por qué falló y cómo lo corregiste. 
Además, debes volver a ejecutar todos los vectores de prueba desde el principio.