# Unidad 3


## 🛠 Fase: Apply

### Actividad 06 🐧  

``` .js
let PASSWORD = ['A', 'B', 'A'];
let key = [];
let keyIndex = 0;
let count = 20;
let startTime;
let state = "Config"; 

let btnA, btnB, btnShake, btnReset;

function setup() {
  createCanvas(400, 400);
  textAlign(CENTER, CENTER);
  textSize(32);
  startTime = millis();

  btnA = createButton('A');
  btnA.position(50, height + 20);
  btnA.mousePressed(() => handleButton('A'));

  btnB = createButton('B');
  btnB.position(100, height + 20);
  btnB.mousePressed(() => handleButton('B'));

  btnShake = createButton('Shake');
  btnShake.position(150, height + 20);
  btnShake.mousePressed(() => handleShake());

  btnReset = createButton('Touch');
  btnReset.position(220, height + 20);
  btnReset.mousePressed(() => handleReset());
}

function draw() {
  background(30);
  fill(255);

  if (state === "Config") {
    text("Modo Configuración", width / 2, 50);
    text(count, width / 2, height / 2);

  } else if (state === "Armed") {
    text("ARMADO", width / 2, 50);
    text(count, width / 2, height / 2);

    if (millis() - startTime > 1000) {
      startTime = millis();
      count--;
      if (count <= 0) {
        state = "Boom";
      }
    }

  } else if (state === "Boom") {
    text("KABOOM", width / 2, height / 2);
  }
}

function handleButton(btn) {
  if (state === "Config") {
    if (btn === 'A') {
      count = min(count + 1, 60);
    } else if (btn === 'B') {
      count = max(10, count - 1);
    }
  } else if (state === "Armed") {
    key[keyIndex] = btn;
    keyIndex++;

    if (keyIndex === PASSWORD.length) {
      let passIsOK = true;
      for (let i = 0; i < PASSWORD.length; i++) {
        if (key[i] !== PASSWORD[i]) 
        {
          passIsOK = false;
          break;
        }
      }

      if (passIsOK) 
      {
        count = 20;
        state = "Config";
      }
      keyIndex = 0;
      key = [];
    }
  }
}

function handleShake() {
  if (state === "Config") {
    startTime = millis();
    state = "Armed";
  }
}

function handleReset() {
  if (state === "Boom") {
    count = 20;
    startTime = millis();
    state = "Config";
  }
}
```

### Actividad 07 🐧  
``` .js
let PASSWORD = ['A', 'B', 'A'];
let key = [];
let keyIndex = 0;
let count = 20;
let startTime;
let state = "Config"; 
let port;
let connectBtn;


function setup() {
  createCanvas(400, 400);
  textAlign(CENTER, CENTER);
  textSize(32);
  startTime = millis();
  
  port = createSerial();
  connectBtn = createButton('Connect to micro:bit');
  connectBtn.position(80, 400);
  connectBtn.mousePressed(connectBtnClick);

  
}

function draw() {
  background(30);
  fill(255);
  
  

  if (state === "Config") {
    text("Modo Configuración", width / 2, 50);
    text(count, width / 2, height / 2);

  } else if (state === "Armed") {
    text("ARMADO", width / 2, 50);
    text(count, width / 2, height / 2);

    if (millis() - startTime > 1000) {
      startTime = millis();
      count--;
      if (count <= 0) {
        state = "Boom";
      }
    }

  } else if (state === "Boom") {
    text("KABOOM", width / 2, height / 2);
  }
}

function handleButton(port) {
  
  if(port.availableBytes() > 0){
        let dataRx = port.read(1);
  if (state === "Config") {
    if (dataRx === 'A') {
      count = min(count + 1, 60);
    } else if (dataRx === 'B') {
      count = max(10, count - 1);
    }
  } else if (state === "Armed") {
    key[keyIndex] = dataRx;
    keyIndex++;

    if (keyIndex === PASSWORD.length) {
      let passIsOK = true;
      for (let i = 0; i < PASSWORD.length; i++) {
        if (key[i] !== PASSWORD[i]) 
        {
          passIsOK = false;
          break;
        }
      }

      if (passIsOK) 
      {
        count = 20;
        state = "Config";
      }
      keyIndex = 0;
      key = [];
    }
    }
  }
}

function handleShake() {
  if (state === "Config") {
    startTime = millis();
    state = "Armed";
  }
}

function handleReset() {
  if (state === "Boom") {
    count = 20;
    startTime = millis();
    state = "Config";
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
Profe, honestamente dudo que el código funcione bien, pero al menos recordé cómo conectarlo al micro:bit y le intenté poner alguito de lógica para adaptar el código.
