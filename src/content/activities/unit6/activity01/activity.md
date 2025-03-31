#### Preparación del entorno y primer contacto

:::note[🎯 Enunciado]
Antes de sumergirnos en el código, vamos a preparar nuestro 
entorno de desarrollo y ejecutar el caso de estudio base. Es crucial entender 
cómo poner en marcha el sistema completo.
:::

:::tip[Recursos]
Necesitarás tener [Node.js](https://nodejs.org/en) instalado en tu sistema. 
Los computadores de la U ya lo tienen, pero si estás trabajando en tu computador personal tendrás 
que instalarlo primero. Esto incluye npm (Node Package Manager).

Descarga o clona el código fuente del caso de estudio desde [este repositorio](https://github.com/juanferfranco/juanferfranco-entagledTest-sfi1-2024-20)
:::

👣 **Pasos**:

:::note[🔧 Configuración del entorno]
- Abre una terminal o símbolo del sistema en tu computador.  
- Navega (usando el comando cd) hasta la carpeta donde descargaste/clonaste el código fuente.  
- Ejecuta el comando: 

    ``` bash
    npm install
    ```  
- Una vez que termine la instalación, ejecuta el comando: 
    
    ``` bash
    npm start
    ```  
- Ten presente que el paso nmp install solo es necesario la primera vez que trabajas con el proyecto.
- Observa la salida en la terminal. Deberías ver un mensaje indicando que el servidor está escuchando.
- Abre tu navegador web preferido.
- En una ventana, escribe la dirección: http://localhost:3000/page1
- En otra ventana del mismo navegador, escribe la dirección: http://localhost:3000/page2
- Interactúa con las ventanas (muévelas si tu sistema operativo lo permite, observa los elementos visuales).
:::

:::note[🧐🧪✍️ Reporta en tu bitácora]

- ¿Qué ocurrió en la terminal cuando ejecutaste npm install? 
¿Cuál crees que es su propósito?  
- ¿Qué mensaje específico apareció en la terminal después de 
ejecutar npm start? ¿Qué indica este mensaje?  
- Describe lo que ves inicialmente en page1 y page2 en tu navegador.
- ¿Qué mensajes aparecieron en la terminal del servidor cuando abriste page1 y page2?
- Describe qué sucede en ambas páginas del navegador cuando mueves una de las 
ventanas. ¿Cambia algo visualmente? ¿Qué mensajes aparecen (si los hay) en la 
consola del navegador (usualmente accesible con F12 -> Pestaña Consola) y en 
la terminal del servidor?
:::

:::caution[📤 Entrega]
Documenta en tu bitácora el proceso de configuración, las salidas de la 
terminal, tus observaciones iniciales del comportamiento de las páginas y los resultados 
de tu primera interacción. 
:::