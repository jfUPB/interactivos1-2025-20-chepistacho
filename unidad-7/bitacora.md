
# Evidencias de la unidad 7

## Explicación importante para la actividad 05 (es muy fácil)


## Actividad 01
### 🧐🧪✍️
- El link que me dejó VS Code fue `https://jmxm6d9k-3000.use2.devtunnels.ms/desktop/`, y es importante usar la IP del computador por lo que se vio en clase. Si hubiera un link general para todos, se modificarían todas las páginas abiertas al mismo tiempo, y mero embale. Con la IP de cada computador, el celular se conectará al computador que uno quiera.
- Sencillo: el `npm install` busca y descarga las dependencias del código **(PREGUNTARLE AL PROFE CÓMO)**, y el `npm start` inicia el servidor donde va a correr el programa que se programó en el `server.js`.
- Realmente no ví ningún mensaje en la versión de escritorio, pero sí en el del celular, que decía "Touch to move the circle".
- Sip, se ven reflejados los cambios en el PC, con cierta latencia, pero sin mayor inconveniente.

## Actividad 02
- Según entendí, el Dev Tunnels es una forma segura (y que nos queda a la mano) para conectar el PC y el cel, pues la conexión directa no sirve para este caso.
- El `TouchMoved` es una función que analiza contínuamente las coordenadas del Touch en el celular, y las pasa al computador como las coordenadas del mouse. El `Treshold`es un límite que imponemos, el cual delimita qué va a considerar el programa como un movimiento que "valga la pena actualizar", evitando al programa actualizar la posición cada movimiento de un pixel.
- Dev Tunnels nos ofrece muchas más opciones y una integración mucho más completa entre herramientas, pero si buscamos fluidez y seguridad, lo mejor será usar la IP.
- Hay un motivo por el cual uno debe hacer ciertas actividades en el momento en que las ve, pues lugo puede ser muy tarde (dije "tomo las fotos en la casa" y no me abrió el programa 😢).

## Actividad 03
- Según entendí (me tocó preguntarle a chat), el `express.static(‘public’)` sirve los archivos **estáticos** automáticamente, lo que sirve para cuando no toca modificar ni aplicar lógica personalizada a los archivos, a diferencia del `app.get(‘/ruta’, …)`, que permite una comunicación entre los archivos, con lógica de por medio.
- Me skipeo esa otra pregunta
- Una de dos (no tengo argumentos para ninguna): o lo recibe el primer dispositivo que conectamos (como si hubiera "agarrado el turno" primero), o lo recibirán ambos al mismo tiempo, porque, la verdad, dudo mucho que haya algún tipo de prioridad al respecto (tipo, "Solo se lo envío a este por ser computador". Eso sería medio racista).
- De lo que recuerdo, mostraba si la pantalla estaba siendo tocada, así como la posición del touch (corroborar en casa).




