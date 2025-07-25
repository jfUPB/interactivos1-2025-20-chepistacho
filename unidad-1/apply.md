# Unidad 1

## 🛠 Fase: Apply

### Actividad 05  
El sistema funciona gracias a un imput (el serial), el cual procesa un código creado por nosotros, y manda una señal virtual como output.  

## Actividad 06  
**Enlace al programa**: https://editor.p5js.org/chepistacho/sketches/nB6RbBTxw  

**Código del programa**:
``` javascript
let port;
let reader;
let x = 200;

function setup() {
  createCanvas(400, 400);

  createButton("Conectar").mousePressed(async () => {
    port = await navigator.serial.requestPort();
    await port.open({ baudRate: 115200 });
    reader = port.readable.getReader();
    leer();
  });
}

function draw() {
  background(220);
  stroke(100)
  fill('orange')
  circle(x, 200, 50);
}

async function leer() {
  while (true) {
    const { value, done } = await reader.read();
    if (done) break;
    let letra = new TextDecoder().decode(value).trim();
    if (letra == "A") x -= 10;
    if (letra == "B") x += 10;
    x = constrain(x, 0, width);
  }
}
```

**Código del micro:bit**:  
``` python
from microbit import *

uart.init(baudrate=115200)

while True:
    if button_a.is_pressed():
        uart.write("A")
    elif button_b.is_pressed():
        uart.write("B")
```
