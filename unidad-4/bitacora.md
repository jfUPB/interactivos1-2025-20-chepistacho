# Evidencias de la unidad 4

## Código

[Enlace a la aplicación a modificar](http://www.generative-gestaltung.de/2/sketches/?01_P/P_1_2_1_01)

Código a modificar:

``` js
'use strict';

var tileCountX = 2;
var tileCountY = 10;

var colorsLeft = [];
var colorsRight = [];
var colors = [];

var interpolateShortest = true;

function setup() {
  createCanvas(800, 800);
  colorMode(HSB);
  noStroke();
  shakeColors();
}

function draw() {
  tileCountX = int(map(mouseX, 0, width, 2, 100));
  tileCountY = int(map(mouseY, 0, height, 2, 10));
  var tileWidth = width / tileCountX;
  var tileHeight = height / tileCountY;
  var interCol;
  colors = [];

  for (var gridY = 0; gridY < tileCountY; gridY++) {
    var col1 = colorsLeft[gridY];
    var col2 = colorsRight[gridY];

    for (var gridX = 0; gridX < tileCountX; gridX++) {
      var amount = map(gridX, 0, tileCountX - 1, 0, 1);

      if (interpolateShortest) {
        // switch to rgb
        colorMode(RGB);
        interCol = lerpColor(col1, col2, amount);
        // switch back
        colorMode(HSB);
      } else {
        interCol = lerpColor(col1, col2, amount);
      }

      fill(interCol);

      var posX = tileWidth * gridX;
      var posY = tileHeight * gridY;
      rect(posX, posY, tileWidth, tileHeight);

      // save color for potential ase export
      colors.push(interCol);
    }
  }
}

function shakeColors() {
  for (var i = 0; i < tileCountY; i++) {
    colorsLeft[i] = color(random(0, 60), random(0, 100), 100);
    colorsRight[i] = color(random(160, 190), 100, random(0, 100));
  }
}

function mouseReleased() {
  shakeColors();
}

function keyPressed() {
  if (key == 'c' || key == 'C') writeFile([gd.ase.encode(colors)], gd.timestamp(), 'ase');
  if (key == 's' || key == 'S') saveCanvas(gd.timestamp(), 'png');
  if (key == '1') interpolateShortest = true;
  if (key == '2') interpolateShortest = false;
}

```

[Enlace a la aplicación modificada](https://editor.p5js.org/chepistacho/sketches/LqdybkkR9)

Código modificado:

``` js

'use strict';

var tileCountX = 2;
var tileCountY = 10;

let port;
let connectBtn;
let connectionInitialized = false;
let microBitConnected = false;
var accelX = 0;
var accelY = 0;
var buttonA = 0;
var buttonB = 0;
var newAState = 0;
var newBState = 0;
let prevA = false;
let prevB= false;


var colorsLeft = [];
var colorsRight = [];
var colors = [];

var interpolateShortest = true;

function setup() {
  createCanvas(800, 800);
  colorMode(HSB);
  noStroke();
  shakeColors();
  port = createSerial();
  connectBtn = createButton('Connect to micro:bit');
  connectBtn.position(80, 300);
  connectBtn.mousePressed(connectBtnClick);

  
}

function draw() {
  
if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
    microBitConnected = false;
  } else {
    microBitConnected = true;
    connectBtn.html("Disconnect");

    if (port.opened() && !connectionInitialized) {
      port.clear();
      connectionInitialized = true;
    }

    if (port.availableBytes() > 0) {
      let data = port.readUntil("\n");
      if (data) {
        data = data.trim();
        let values = data.split(",");
        if (values.length == 4) {
          accelX = int(values[0]) + windowWidth / 2;
          accelY = int(values[1]) + windowHeight / 2;
          buttonA = values[2].toLowerCase() === "true";
          buttonB = values[3].toLowerCase() === "true";
          updateButtonStates(buttonA, buttonB);
        } else {
          print("No se están recibiendo 4 datos del micro:bit");
        }
      }
    }
  }
  
  tileCountX = int(map(accelX, 0, width, 2, 100));
  tileCountY = int(map(accelY, 0, height, 2, 10));
  var tileWidth = width / tileCountX;
  var tileHeight = height / tileCountY;
  var interCol;
  colors = [];

  for (var gridY = 0; gridY < tileCountY; gridY++) {
    var col1 = colorsLeft[gridY];
    var col2 = colorsRight[gridY];

    for (var gridX = 0; gridX < tileCountX; gridX++) {
      var amount = map(gridX, 0, tileCountX - 1, 0, 1);

      if (interpolateShortest) {
        // switch to rgb
        colorMode(RGB);
        interCol = lerpColor(col1, col2, amount);
        // switch back
        colorMode(HSB);
      } else {
        interCol = lerpColor(col1, col2, amount);
      }

      fill(interCol);

      var posX = tileWidth * gridX;
      var posY = tileHeight * gridY;
      rect(posX, posY, tileWidth, tileHeight);

      // save color for potential ase export
      colors.push(interCol);
    }
  }
}

function updateButtonStates(buttonA, buttonB) {
  if (buttonA){
    shakeColors();
  }
  if (buttonB){
    interpolateShortest = !interpolateShortest;
  }
}

function shakeColors() {
  for (var i = 0; i < tileCountY; i++) {
    colorsLeft[i] = color(random(0, 60), random(0, 100), 100);
    colorsRight[i] = color(random(160, 190), 100, random(0, 100));
  }
}




function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200);
    } else {
        port.close();
    }
}


```

## Video

[Video demostratativo](https://www.youtube.com/watch?v=4Z5-cDs31q0)


Disclaimer:  
Las variables son distintas a las del profe porque lo intenté hacer usando IA, pero luego me dí cuenta de que el código del profe era más sencillo que lo que me proporcionaba GPT, entonces dejé las variables como las tenía y las puse en ese código. Si hay algún pedazo de código que no sirve para nada, seguramente me faltó borrarlo de cuando lo hice con inteligencia artificial.  
Por lo mismo (creo yo), pasa que el código se detiene después de unos segundos de uso. Eso no se alcanza a apreciar en el video porque estuve de buenas, pero sí me pasó un par de veces mientras lo probaba.




