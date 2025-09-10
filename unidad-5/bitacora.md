
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

