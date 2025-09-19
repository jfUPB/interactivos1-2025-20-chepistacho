
# Evidencias de la unidad 5
## Actividad 01 🐧  
- **Describe cómo se están comunicando el micro:bit y el sketch de p5.js. ¿Qué datos envía el micro:bit?**: Se comunican por el código del micro:bit, y el p5.js lo recibe gracias a funciones de una biblioteca que agregamos en el sketch. Se envían y reciben datos como la orientación del micro:bit, o cuando se presionan y liberan los botones.
- **¿Cómo es la estructura del protocolo ASCII usado?**: 😕💀.
- **Muestra y explica la parte del código de p5.js donde lee los datos del micro:bit y los transforma en coordenadas de la pantalla.**:
``` js
if (microBitAState === true) {
        let x = microBitX;
        let y = microBitY;

        if (keyIsPressed && keyCode === SHIFT) {
          if (abs(clickPosX - x) > abs(clickPosY - y)) {
            y = clickPosY;
          } else {
            x = clickPosX;
          }
        }

        push();
        translate(x, y);
        rotate(radians(angle));
        if (lineModuleIndex != 0) {
          tint(c);
          image(
            lineModule[lineModuleIndex],
            0,
            0,
            lineModuleSize,
            lineModuleSize
          );
        }
```
- **¿Cómo se generan los eventos A pressed y B released que se generan en p5.js a partir de los datos que envía el micro:bit?**: Se tienen dos variables para cada botón, que se actualizan con cada cambio (cuando se presiona o cuando se libera). Ej: si anteriormente se había presionado, y posteriormente se libera, el programa compara estos dos estados, y como son diferentes lo detecta como un cambio.
- **Capturas de pantalla de los algunos dibujos que hayas hecho con el sketch.**: <img width="1370" height="1238" alt="image" src="https://github.com/user-attachments/assets/a0d3a83b-4ce2-48cd-ad6e-e0b8237c5cf1" />

## Actividad 02 🐧 
🧐🧪✍️ La primera vez que capturé el resultado, me había tirado un resultado normal, pero ahora me tira esto <img width="1007" height="211" alt="image" src="https://github.com/user-attachments/assets/f939dd45-9234-4f7e-bbea-1fb1fd812bdb" /> (no sé por qué pasa esto).

🧐🧪✍️ Con todo en HEX sí me tira algo normal, similar a lo que me había tirado la última vez que lo había intentado (culpa mía por no haberlo capturado en su momento). <img width="965" height="196" alt="image" src="https://github.com/user-attachments/assets/797bb55c-a8c5-4817-982f-b85fc2864b4e" />.  
Me imagino que se relaciona con la línea de código debido a que está mandando un paquete de datos

🧐🧪✍️ Siento que la ventaja con respecto al texto en ASCII es que ahora se puede saber cómo el computador está leyendo la info.

Por motivos que desconozco, el Serial Terminal no siguió funcionando.



## Actividad 03 🐧

La primera versión funcionaba muy raro (empezaba a pintar cuando presionaba la A en alguna otra pestaña y volvía a p5), y no respondía al micro:bit 😢  
<img width="738" height="691" alt="image" src="https://github.com/user-attachments/assets/6bb64a66-432b-486b-b684-56497efc5162" />  
Es como si el PC no reconociera el micro:bit, por más que ya está conectado (el serial terminal tampoco reacciona a los cambios que hago en el micro:bit)  

La segunda versión me tiró un error, pero la tercera sí me funcionó


## Consolidación  
**Criterio 1:** Aquí siento que queda **En Desarrollo**, por ahí en un `3.1`, como se puede ver en el desarrollo de la actividad 2.
**Criterio 2:** Yo digo que aquí caigo en "**Logrado**", por el hecho de que, si bien no desarrollé todas las actividades, las que hice pueden considerarse como "cumplidoras". `3.7`
**Criterio 3:** También **En Desarrollo**. Las actividades concluyen cosas muy evidentes, sin mucho análisis. Aquí pongo un `3.4`
**Criterio 4:** **Inicial**, tirando para nada. No llegué a hacer las actividades donde más se mostraban esos conceptos, pero, haciendo esta autoevaluación, me puse a hacer la tarea, al menos para suplir parcialmente los conceptos que no llegué a profundizar durante la unidad. `1.1`

**Nota final:** 2.8


























































































































































































































































































































