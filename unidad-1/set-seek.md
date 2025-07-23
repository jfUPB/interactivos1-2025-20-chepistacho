# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 01
**¿Qué es un sistema físico interactivo?**: Un sistema físico interactivo es una serie de componentes eléctricos que responden a estímulos reales con un fin determinado.  
**¿Cómo podrías aplicar lo que has visto en tu perfil profesional?**: Creando sistemas para mejorar la experiencia de usuario al interactuar con mis productos.  


### Actividad 02
**¿Qué es el diseño/arte generativo?**  
Es una práctica, en la que un artista da instrucciones y reglas a un sistema autónomo para crear arte

**¿Cómo podrías aplicar lo que has visto en tu perfil profesional?**  
Para obtener una experiencia más inmersiva y completa a la hora de crear cualquier producto

### Actividad 03
**Inputs**: Serial, código, botones  
**Proceso**: Programa  
**Output**: Encendido o apagado de las luces LED


### Actividad 04  
**Enlace**: https://editor.p5js.org/chepistacho/sketches/7viX8XRe0  

``` javascript
function setup() {
  createCanvas(400, 400);
  background(0);
  noStroke();
}

function draw() {
  background(0, 15);

  
  let r = map(sin(frameCount * 0.01), -1, 1, 0, 80);
  let g = map(cos(frameCount * 0.015), -1, 1, 0, 80);
  let b = random(180, 255);

  
  let t = frameCount * 0.05;
  let x = 200 * cos(t) + 200;
  let y = 66 * sin(t * 2) + 200;

  
  fill(r, g, b, 60);
  circle(x, y, 30);

  
}
```
<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/e3b5f417-68d0-4e31-9905-b46344d8469f" />  

**Nota**: Para la realización de este programa, hice uso de la documentación de p5.js, así como de herramientas de IA para lograr un mejor resultado y una mejor comprensión del funcionamiento del mismo.

