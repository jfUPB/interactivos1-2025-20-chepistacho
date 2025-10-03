
# Evidencias de la unidad 6
## Configuración de la unidad  
1. Instalar Node.js y Git
2. Clonar el repositorio
3. Abrir Git Bash en la carpeta donde vaya a trabajar
4. Poner el comando `pwd`
5. Ahora el comando `git clone https://github.com/juanferfranco/entangledTest-sfi1-2025-20.git`
6. `ls`
7. `cd entangledTest-sfi1-2025-20/`
8. Hacemos caso a los comandos que están en la página del curso (al principio)

## Actividad 1 🐧
- **¿Qué ocurrió en la terminal cuando ejecutaste npm install? ¿Cuál crees que es su propósito?**: Se descargaron los archivos del repositorio para poderse ejecutar.
- **¿Qué mensaje específico apareció en la terminal después de ejecutar npm start? ¿Qué indica este mensaje?**: `All clients are fully synced`, y me imagino que significa que todo se inició sin problema con el repositorio.
- **Describe lo que ves inicialmente en page1 y page2 en tu navegador**: Hay una bola roja en cada página, que parecieran estar conectadas por un cable que traspasa la ventana del navegador.
- **¿Qué mensajes aparecieron en la terminal del servidor cuando abriste page1 y page2?**: `Sync status: SYNCED`.
- **Describe qué sucede en ambas páginas del navegador cuando mueves una de las ventanas. ¿Cambia algo visualmente? ¿Qué mensajes aparecen (si los hay) en la consola del navegador (usualmente accesible con F12 -> Pestaña Consola) y en la terminal del servidor?**: Se repite el mismo mensaje de sincronización, pero se actualiza la información de posición del objeto que se está moviendo, como si se fuera actualizando en tiempo real (pues, sí está pasando así).

## Actividad 2 🐧  

- **¿Qué es Internet?**: En mi día a día uso internet casi todo el tiempo. Si se cortara esa "rampa", quedo embalado para prácticamente todo, desde la comunicación con mi familia, el tiempo de ocio en redes sociales y la realización de trabajos de la U (No podría estar haciendo esta bitácora, por ejemplo).
- **Navegador y servidor**: Yo creo que el servicio cliente-servidor más evidente que hay en mi día a día es a la hora de pedir café en las máquinas expendedoras, donde, idealmente, el cliente (yo) pide café y el servidor (la máquina) lo entrega (normalmente me sirve agua caliente).
- **¿Qué es una URL?**: Mi página favorita es el MarketPlace de Facebook, y la URL es `https://www.facebook.com/marketplace/`. El protocolo es el `https://` de toda la vida, el dominio es `www.facebook.com`, y la ruta específica es `/marketplace/`. Sin la ruta específica, me manda a la página de inicio de Facebook.
- **Protocolo HTTP**: Me imagino que el cambio en la comlejidad de los protocolos radica en que ahora no se trata de enviar señales concretas por medio de un cable, sino que se debe localizar la información en un servidor, para mandarla a un cliente en el momento en el que lo pida.
- x

## Actividad 3 🐧
### 🧐🧪✍️ Experimento 1
Cuando cambié la ruta, ya no permite acceder al link original, pues muestra un mensaje de error, pero cuando pusimos `http://localhost:3000/pagina_uno`, ahí sí abrió, mostrando la misma página que antes mostraba el link original.  
<img width="132" height="26" alt="image" src="https://github.com/user-attachments/assets/23cd6315-a495-4829-adbf-d7845a948690" />  
(Mensaje de error)  

Esto indica que esa misma línea de código es la que reconoce la ruta específica en la que debe mostrar la página, es decir, si esa parte del código dijera `fisicos1`, la URL para acceder sería `http://localhost:3000/fisicos1`.

### 🧐🧪✍️ Experimento 2
Al iniciar la primera página, sale lo siguiente:  
``` js
Server is listening on http://localhost:3000
A user connected - ID: X2RQlWev7Xz5IjAHAAAB
Received win1update from ID: X2RQlWev7Xz5IjAHAAAB Data: { x: 352, y: 333, width: 537, height: 275 }
Debug - Connected clients: 1, Page1: 1, Page2: 0, Synced: 0
Sync status: pages=false, synced=false, clients=1
Debug - Connected clients: 1, Page1: 1, Page2: 0, Synced: 1
Sync status: pages=false, synced=true, clients=1

```
Al abrir la segunda, tira el mismo ID, así como cuando cerré solo la primera página, pero cuando cerré ambas, cambió el ID .

### 🧐🧪✍️ Experimento 3
Básicamente, lo que muestra son los cambios de tamaño y posición de cada página, indicándolo con su respectivo ID.
Cuando cambio el código a `socket.emit(‘getdata’, page1)`, las ventanas dejan de estar sincronizadas, me imagino que porque el `broadcast` es el que "transmite" la info entre las dos páginas, mieentras que, sin el `broadcast`, se envía la info al mismo cliente que lo emitió.

### 🧐🧪✍️ Experimento 4
Entonces, básicamente, el localhost indica el número de puerto que estará escuchando el código, por lo que debe de coincidir del del server.js con el de la URL.

## Actividad 4 🐧
### 🧐🧪✍️ Experimeto 1
Efectivamente, tira un error relaconado con la conexión, específicamente este:  
<img width="498" height="45" alt="image" src="https://github.com/user-attachments/assets/581ce46f-782c-452f-ab5b-2d47ab3f74fc" />  
Y, cuando vuelvo a correr el servidor, dejan de aparecer los errores.

### 🧐🧪✍️ Experimeto 2
Básicamente, lo que hicimos fue comentar la línea que permite la comunicación entre las dos páginas, por lo que la página se quedará cargando por los siglos de los siglos, y no se actualizará.

### 🧐🧪✍️ Experimeto 3
Básicamente, muestra data de este estilo constantemente:  
<img width="632" height="77" alt="image" src="https://github.com/user-attachments/assets/4c8e1787-a942-4c71-9f6a-221bdc95f75e" />  
Mostrando en tiempo real la data que le llega a la página 2.  


### 🧐🧪✍️ Experimeto 4















