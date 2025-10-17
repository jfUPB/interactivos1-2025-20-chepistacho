
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

## Actividad 05
Mi idea es usar la canción "Human Nature" de Michael Jackson, la cual habla sobre la vida nocturna, y hace referencia a la ciudad de Nueva York (temas que siempre me han parecido interesantes).  
La idea de las visuales es mostrar un skyline reconocible de esta ciudad, el cual, por medio de controladores, irá cambiando su aspecto (cantidad de luces encendidas y su color), así como que irán reaccionando a la música en tiempo real. Además, dado que en una parte de la canción habla sobre el amanecer, mi idea es tener un controlador adicional para que, cuando llegue esa parte, pueda hacer una transición limpia de la noche al día.

### Desktop  
**Sketch.js**  
``` js
let song;
let fft;
let stars = [];
let skyline = [];
let startTime;
let chryslerWindows = [];
let currentState = "intro"; // intro, verso1, coro, verso2, puente, amanecer, outro
let cfg;
let socket;
const port = 3000;
let audioStarted = false;
let nightColor, dawnColor;
let transitionFactor = 0;


const estados = {
  intro: {
    colorLuz: [224, 224, 255],   // blanco frío
    densidadVentanas: 0.02       // ciudad casi dormida
  },
  verso1: {
    colorLuz: [191, 166, 90],    // dorado cálido
    densidadVentanas: 0.07
  },
  coro: {
    colorLuz: [250, 230, 180],   // blanco cálido
    densidadVentanas: 0.12
  },
  verso2: {
    colorLuz: [255, 128, 171],   // rosado fucsia
    densidadVentanas: 0.15
  },
  puente: {
    colorLuz: [255, 213, 79],    // dorado verdoso
    densidadVentanas: 0.09
  },
  amanecer: {
    colorLuz: [255, 180, 120],   // anaranjado suave
    densidadVentanas: 0.05
  },
  outro: {
    colorLuz: [255, 255, 255],   // blanco tenue
    densidadVentanas: 0.02
  }
};




function preload() {
  song = loadSound("HumanNature.mp3");
}

function setup() {
    createCanvas(windowWidth, windowHeight);



    socket = io();

    socket.on('connect', () => {
        console.log('✅ Connected to server');
    });

    // Caso 1: el servidor emite un evento explícito 'slider' con value (recomendado)
    socket.on('slider', (value) => {
        console.log('🎚 slider event received:', value);
        // Si tu slider manda 0..100 lo normalizamos a 0..1
        transitionFactor = (typeof value === 'number') ? constrain(value / 100, 0, 1) : transitionFactor;
    });

    // Caso 2: el servidor emite mensajes genéricos tipo { type: 'slider', value: X } o { type: 'cambiarEstado', ... }
    // Mantenemos tu handler 'message' pero lo hacemos multiuso
    socket.on('message', (msg) => {
        console.log('📩 Mensaje recibido (message):', msg);

        if (!msg) return;

        // Si el servidor manda objetos JSON stringificados, parseamos (seguro y silencioso)
        if (typeof msg === 'string') {
            try {
                msg = JSON.parse(msg);
            } catch (e) {
                // no es JSON, dejamos como está
            }
        }

        if (msg.type === 'cambiarEstado') {
            cambiarEstado(msg.value);
            return;
        }

        if (msg.type === 'slider') {
            // Ej: { type: 'slider', value: 72 }
            const v = msg.value;
            if (typeof v === 'number') {
                transitionFactor = constrain(v / 100, 0, 1);
                console.log('🎚 slider (from message) -> transitionFactor:', transitionFactor);
            }
            return;
        }

        // fallback: si el servidor manda simplemente { slider: 72 } o { value: 72 }
        if (typeof msg === 'object') {
            if (typeof msg.value === 'number' && (msg.channel === 'slider' || msg.slider === undefined && msg.type === undefined)) {
                // Casi un parche, no lo uses si tu servidor manda otras cosas
                transitionFactor = constrain(msg.value / 100, 0, 1);
                console.log('🎚 slider (fallback) ->', transitionFactor);
                return;
            }
        }

        console.log('⚠️ Tipo de mensaje no reconocido:', msg.type ?? '(sin type)');
    });




    socket.on('disconnect', () => {
        console.log('Disconnected from server');
    });

    socket.on('connect_error', (error) => {
        console.error('Socket.IO error:', error);
    });

  fft = new p5.FFT();
  song.play();
  startTime = millis();

  // 🌠 Estrellas
  for (let i = 0; i < 180; i++) {
    stars.push({
      x: random(width),
      y: random(height / 1.7),
      size: random(1, 3),
      flickerSpeed: random(0.15, 0.4),
      offset: random(1000),
      baseBrightness: random(150, 240)
    });
  }

      // 🏙️ Skyline base (ajustado visualmente)
      let baseY = height;
      let centerX = width / 2;
      skyline = [
        { x: centerX - 950, w: 180, h: 280 },
        { x: centerX - 850, w: 150, h: 260 },
        { x: centerX - 690, w: 130, h: 310 },
        { x: centerX - 530, w: 125, h: 360 },
        { x: centerX - 390, w: 120, h: 310 },
        { x: centerX - 250, w: 115, h: 380 },
        { x: centerX - 120, w: 100, h: 430 },

        { x: centerX,       w: 90,  h: 500, spire: true }, // 🏙️ Empire State
        { x: centerX + 120, w: 100, h: 420 },
        { x: centerX + 240, w: 115, h: 370 },
        { x: centerX + 370, w: 120, h: 320 },
        { x: centerX + 500, w: 130, h: 280 },
        { x: centerX + 640, w: 145, h: 260 },
        { x: centerX + 800, w: 170, h: 290 }
      ];
  
        secondarySkyline = [];
      let baseY2 = height;
      let centerX2 = width / 2;
      let depthFactorSecondary = 0.4; // 👈 un poco “más atrás” visualmente

      // edificios secundarios, más pequeños y distribuidos
      let secondaryData = [
        { dx: -800, w: 110, h: 220 },
        { dx: -600, w: 90, h: 250 },
        { dx: -400, w: 100, h: 200 },
        { dx: -170, w: 80, h: 230 },
        { dx:  70, w: 85, h: 240 },
        { dx:  450, w: 95, h: 210 },
        { dx:  600, w: 120, h: 260 },
        { dx:  800, w: 100, h: 220 },
      ];

      for (let s of secondaryData) {
        secondarySkyline.push({
          x: centerX2 + s.dx,
          w: s.w,
          h: s.h,
          depth: depthFactorSecondary
        });
      }

    for (let b of secondarySkyline) {

  b.windows = [];
  let cols = int(b.w / 6);
  let rows = int(b.h / 8);
  let startX = b.x + 3;
  let startY = baseY2 - b.h + 6;

  for (let i = 0; i < cols; i++) {
    for (let j = 0; j < rows; j++) {
      if (random() > 0.94) {  // 👈 menos densidad de ventanas que el skyline principal
        b.windows.push({
          x: startX + i * 6,
          y: startY + j * 8
        });
      }
    }
  }
}

// 🪟 Ventanas del Chrysler Building (solo en el rectángulo)
let chryslerX = width / 2 - 240;
let chryslerH = 390;
let bodyW = 100;
let colsC = int(bodyW / 6);
let rowsC = int(chryslerH / 8);
let startXc = chryslerX - bodyW / 2 + 3;
let startYc = height - chryslerH + 6;

for (let i = 0; i < colsC; i++) {
  for (let j = 0; j < rowsC; j++) {
    if (random() > 0.94) {  // 👈 densidad baja
      chryslerWindows.push({
        x: startXc + i * 6,
        y: startYc + j * 8
      });
    }
  }
}

  // 💡 Ventanas con tiempos de encendido y color propio
  skyline.forEach(b => {
    b.windows = [];
    let cols = int(b.w / 6);
    let rows = int(b.h / 8);
    let startX = b.x + 3;
    let startY = height - b.h + 6;
    for (let i = 0; i < cols; i++) {
      for (let j = 0; j < rows; j++) {
        if (random() > 0.95) {
          let initialColor = estados[currentState].colorLuz;
          b.windows.push({
            x: startX + i * 6,
            y: startY + j * 8,
            on: false,
            targetOn: true,
            turnOnTime: random(1000, 10000),
            transitionTime: random(0, 4000),
            color: [...initialColor],
            targetColor: [...initialColor],
            brightness: random(120, 200)
          });
        }
      }
    }
  });

}

function draw() {
    let baseNight = color(10, 15, 35);
    let dawn = color(255, 140, 80); // naranja cálido
    let bg = lerpColor(baseNight, dawn, transitionFactor);
    background(bg);
  let cfg = estados[currentState];
  

    if (audioStarted) {
      let spectrum = fft.analyze();
      let trebleEnergy = fft.getEnergy("treble");
      let midEnergy = fft.getEnergy("mid");
      let bassEnergy = fft.getEnergy("bass");


      // 🌠 Estrellas titilantes con brillo dependiente del estado
        noStroke();
        blendMode(ADD);
        for (let s of stars) {
            let flicker = sin(frameCount * s.flickerSpeed + s.offset) * map(trebleEnergy, 0, 255, 0.6, 2.5);
            let brightness = constrain(s.baseBrightness + flicker * 80, 100, 255);

            // 🌄 Las estrellas se apagan según transitionFactor
            brightness *= (1 - transitionFactor); // 0 = noche, 1 = día

            fill(brightness);
            circle(s.x, s.y, s.size + random(0.5));
        }


      // ✨ Dibujar Chrysler Building (detrás del skyline)
      drawChrysler(midEnergy);
  
      drawOWTC(midEnergy);
  
        // 🏙️ Skyline secundario
        
    drawSecondarySkyline(bassEnergy);


      // 🏙️ Dibujar skyline
      blendMode(BLEND);
      noStroke();
      rectMode(CORNER)
      let baseY = height;
      for (let b of skyline) {
        // gradiente sutil
        let baseY = height;
        noStroke();
        fill(15); // Color opaco del edificio
        rect(b.x, baseY - b.h, b.w, b.h);

        // 🌆 Empire State Building
        if (b.spire) {
      let cx = b.x + b.w / 2;
      let top = baseY - b.h;

      push();
      rectMode(CENTER);
      noStroke();

      let sections = [
        { offset: 0,    w: b.w * 0.72, h: 25 },
        { offset: -25,  w: b.w * 0.62, h: 22 },
        { offset: -47,  w: b.w * 0.45, h: 35 },
        { offset: -82,  w: b.w * 0.3,  h: 38 },
        { offset: -120, w: b.w * 0.18, h: 35 }
      ];

      // 🏙️ Dibujar secciones escalonadas
      fill(25);
      for (let s of sections) {
        let y = top + s.offset - s.h / 2;
        rect(cx, y, s.w, s.h);
      }

      // ✨ Glow por encima de las secciones
      let glowIntensity = map(midEnergy, 0, 255, 30, 160);
      for (let s of sections) {
        let y = top + s.offset - s.h / 2;
        let glowHeight = s.h * 1.3;
        for (let g = 0; g < glowHeight; g += 2) {
          let alpha = map(g, 0, glowHeight, glowIntensity, 0);
          fill(cfg.colorLuz[0], cfg.colorLuz[1], cfg.colorLuz[2], alpha);

          rect(cx, y + s.h / 2 - g, s.w * 0.9, 2);
        }
      }

      // 🗼 Antena
      stroke(230);
      strokeWeight(1.2);
      let topSection = sections[sections.length - 1];
      let spireBase = top + topSection.offset - topSection.h;
      let spireTop = spireBase - 50;
      line(cx, spireBase, cx, spireTop);

      pop();
    }
      }

      // 💡 Ventanas encendiéndose
      noStroke();
    let elapsed = millis();

    for (let b of skyline) {
      for (let w of b.windows) {
        // --- Seguridad / inicialización si falta alguna propiedad ---
        if (!w.color || w.color.length !== 3) {
          // si no tiene color, inicializamos con el color del estado actual
          let base = estados[currentState].colorLuz;
          w.color = [base[0], base[1], base[2]];
        }
        if (!w.targetColor || w.targetColor.length !== 3) {
          w.targetColor = [w.color[0], w.color[1], w.color[2]];
        }
        if (typeof w.targetOn === 'undefined') w.targetOn = true;
        if (typeof w.transitionTime === 'undefined') w.transitionTime = millis(); // sin delay por defecto
        if (typeof w.turnOnTime === 'undefined') w.turnOnTime = millis() + random(0, 2000);

        // --- lógica de encendido/apagado con tiempos ---
        if (!w.on && elapsed > w.turnOnTime) w.on = true;
        if (w.targetOn && !w.on && millis() > w.transitionTime) w.on = true;
        if (!w.targetOn && w.on && millis() > w.transitionTime) w.on = false;

        // --- interpolación segura de color hacia targetColor ---
          if (!w.keepColor) {
            // solo las nuevas ventanas interpolan color
            w.color[0] = lerp(w.color[0], w.targetColor[0], 0.05);
            w.color[1] = lerp(w.color[1], w.targetColor[1], 0.05);
            w.color[2] = lerp(w.color[2], w.targetColor[2], 0.05);
          }


        // --- dibujar si está encendida ---
        if (w.on) {
            let pulse = map(sin(frameCount * 0.05 + w.x * 0.01), -1, 1, 0.8, 1.2);
            let alpha = map(midEnergy, 0, 255, 60, 150) * pulse;

            // Fade-out controlado por slider: cuando slider sube, alpha baja
            // factorFade va de 1 (slider=0) a 0.1 (slider=1) — ajustalo si querés otro rango
            let factorFade = lerp(1, 0.1, transitionFactor);
            let fadedAlpha = alpha * factorFade;

            fill(
                constrain(w.color[0], 0, 255),
                constrain(w.color[1], 0, 255),
                constrain(w.color[2], 0, 255),
                fadedAlpha
            );
            rect(w.x, w.y, 3, 4);

        }
      }

      // limpieza opcional: dejar solo ventanas que estén on o tengan targetOn
      b.windows = b.windows.filter(w => w.on || w.targetOn);
    }
  
    }
}
// 🏛️ Chrysler Building
function drawChrysler(midEnergy) {
  push();
  blendMode(BLEND); // mezcla normal
  

  let chryslerX = width / 2 - 240;
  let chryslerBaseY = height;
  let chryslerH = 390;
  let domeLevels = 6;
  let bodyW = 100;
  let depthFactor = 0.3; // <— controla cuán “atrás” se ve (1 = original)

  // === Domo ===
  let domeBaseY = chryslerBaseY - chryslerH;
  let prevTopY = domeBaseY;
  let domes = [];

  for (let i = 0; i < domeLevels; i++) {
    let t = i / (domeLevels - 1);
    let inv = 1 - t;

    let levelW = bodyW * (1 - 0.17 * i);
    let levelH = 40 * (1 + inv * 0.5);
    let curvature = 1.2 - t * 0.5;

    let y = prevTopY + 1;
    let topY = y - levelH * curvature;

    let r = map(i, 0, domeLevels - 1, 220, 140);
    let g = map(i, 0, domeLevels - 1, 230, 160);
    let b = map(i, 0, domeLevels - 1, 250, 180);

    domes.push({ levelW, levelH, y, topY, r, g, b });
    prevTopY = y - levelH * 0.47;
  }

  // === Cuerpo principal ===
  let baseColor = domes[0];
  noStroke();
  fill(
    baseColor.r * depthFactor,
    baseColor.g * depthFactor,
    baseColor.b * depthFactor
  );
  rectMode(CORNER);
  rect(chryslerX - bodyW / 2, chryslerBaseY - chryslerH, bodyW, chryslerH);

  // === Antena ===
  stroke(255 * depthFactor);
  strokeWeight(1);
  let topMost = domes[domes.length - 1];
  let anchorY = topMost.y;
  line(chryslerX, anchorY, chryslerX, anchorY - 75);

  // === Ventanas Chrysler ===
let bassEnergy = fft.getEnergy("bass");  // usar bajos
let glow = map(bassEnergy, 0, 255, 30, 180);

for (let w of chryslerWindows) {
  let pulse = map(sin(frameCount * 0.05 + w.x * 0.01), -1, 1, 0.7, 1.3);
  let alpha = glow * pulse;

  // ✨ Parpadeo aleatorio para hacerlo menos rígido
  if (random() < 0.005) alpha = 0;
  let cfg = estados[currentState];
  fill(cfg.colorLuz[0], cfg.colorLuz[1], cfg.colorLuz[2], alpha);
  noStroke();
  rect(w.x, w.y, 3, 4);
}
  // === Dibujar domo ===
  noStroke();
  for (let j = domeLevels - 1; j >= 0; j--) {
    let d = domes[j];
    fill(
      d.r * depthFactor,
      d.g * depthFactor,
      d.b * depthFactor
    );

    beginShape();
    vertex(chryslerX - d.levelW / 2, d.y);
    bezierVertex(
      chryslerX - d.levelW * 0.35, d.topY,
      chryslerX + d.levelW * 0.35, d.topY,
      chryslerX + d.levelW / 2, d.y
    );
    endShape(CLOSE);
    
    
    // === Ventanas triangulares mejor distribuidas ===
    {
  let numWindows = int(map(d.levelW, 60, 140, 2, 8));
  numWindows = max(0, numWindows);

  let baseMargin = 0.2;
  let compression = map(d.levelW, 60, 140, 0.4, 1.0);

  // ⚡️ Ajuste adicional para niveles con pocas ventanas
  if (numWindows === 2) compression *= 0.7;
  else if (numWindows === 3) compression *= 0.85;

  let effectiveSpan = (1 - 2 * baseMargin) * compression;
  let startT = 0.5 - effectiveSpan / 2;
  let endT   = 0.5 + effectiveSpan / 2;

  let triW = (d.levelW / (numWindows + 1)) * 0.4 * 1.2;
  let triH = d.levelH * 0.25 * 0.7;
  let eps = 0.001;

  const curvePoint = (t) => {
    let x = lerp(chryslerX - d.levelW / 2, chryslerX + d.levelW / 2, t);
    let relative = (1 - 4 * (t - 0.5) * (t - 0.5));
    let baseY = d.y - (d.y - d.topY) * relative;
    let y = lerp(baseY, d.topY, 0.2);
    return { x, y };
  };

  if (numWindows > 0) {
    for (let i = 0; i < numWindows; i++) {
      let t = numWindows === 1
        ? 0.5
        : map(i, 0, numWindows - 1, startT, endT);

      let p = curvePoint(t);
      let p2 = curvePoint(constrain(t + eps, 0, 1));

      let tx = p2.x - p.x;
      let ty = p2.y - p.y;
      let tlen = sqrt(tx * tx + ty * ty) || 1;
      tx /= tlen;
      ty /= tlen;

      let nx = -ty;
      let ny = tx;
      if (ny > 0) {
        nx = -nx;
        ny = -ny;
      }

      let halfW = triW / 2;
      let bx1 = p.x - tx * halfW;
      let by1 = p.y - ty * halfW;
      let bx2 = p.x + tx * halfW;
      let by2 = p.y + ty * halfW;
      let ax = p.x + nx * triH;
      let ay = p.y + ny * triH;

      if (!isFinite(bx1) || !isFinite(by1) || !isFinite(bx2) || !isFinite(by2) || !isFinite(ax) || !isFinite(ay)) continue;

      let cfg = estados[currentState]; // si no lo tenés definido antes en la función
      let glow = map(midEnergy, 0, 255, 150, 240);
      let flicker = random(-20, 20);
      fill(
        cfg.colorLuz[0],
        cfg.colorLuz[1],
        cfg.colorLuz[2],
        constrain(glow + flicker, 80, 255)
      );

      noStroke();

      triangle(bx1, by1, bx2, by2, ax, ay);
    }
  }
}
    
  }

  
  
  pop();
}

// 🏢 One World Trade Center
function drawOWTC(bassEnergy) {
  let cfg = estados[currentState];
  push();
  blendMode(BLEND);

  let owtx = width / 2 + 240;  // 👉 posición horizontal
  let baseY = height;
  let towerH = 500;
  let baseW = 100;
  let topW = 75;
  let spireH = 40;       // altura de la antena
  let glassH = 140;      // altura del triángulo frontal
  let capW = topW * 0.8;
  let capH = 10;         // altura del rectángulo superior
  let depthFactor = 0.3; // para empujarlo “atrás” un poco

  // === Torre principal (forma trapezoidal) ===
  noStroke();
  fill(180 * depthFactor, 200 * depthFactor, 230 * depthFactor);
  beginShape();
  vertex(owtx - baseW / 2, baseY);
  vertex(owtx + baseW / 2, baseY);
  vertex(owtx + topW / 2, baseY - towerH);
  vertex(owtx - topW / 2, baseY - towerH);
  endShape(CLOSE);

  // === Antena ===
  stroke(255 * depthFactor);
  strokeWeight(1.5);
  line(owtx, baseY - towerH, owtx, baseY - towerH - 80);
  
  
  
  // === Triángulo frontal ===
  fill(120 * depthFactor, 140 * depthFactor, 180 * depthFactor);
  triangle(
    owtx - topW / 2, baseY - towerH,    // esquina superior izquierda (inicio base)
    owtx + topW / 2, baseY - towerH,    // esquina superior derecha (fin base)
    owtx, baseY                         // punta inferior
  );
  
  // === Rectángulo superior (techo) ===
  fill(160 * depthFactor, 180 * depthFactor, 210 * depthFactor);
  rectMode(CENTER);
  rect(owtx, baseY - towerH - capH / 2, capW, capH);
  
  // ✨ Glow suave y dinámico sobre el rectángulo superior (OWTC)
  let owGlowBaseIntensity = map(bassEnergy, 0, 255, 80, 180);
  let dynamicGlowHeight = map(bassEnergy, 0, 255, 10, 50); // altura del glow
  let flicker = random(-8, 8); // parpadeo leve

  for (let g = 0; g < dynamicGlowHeight; g += 2) {
    let alpha = map(g, 0, dynamicGlowHeight, owGlowBaseIntensity + flicker, 0);
    stroke(cfg.colorLuz[0], cfg.colorLuz[1], cfg.colorLuz[2], constrain(alpha, 0, 255));
    strokeWeight(1);
    line(
      owtx - capW * 0.4,
      baseY - towerH - g,
      owtx + capW * 0.4,
      baseY - towerH - g
    );
  }

  pop();
}

function drawSecondarySkyline(bassEnergy) {
  let baseY = height;
  noStroke();
  rectMode(CORNER);

  // 💥 Intensidad de las luces sincronizada con los bajos
  let glow = map(bassEnergy, 0, 255, 30, 180);

    for (let b of secondarySkyline) {
        blendMode(BLEND);
      let brightness = map(b.depth, 0.5, 1, 55, 35); // más moderado
      fill(brightness * 0.8, brightness * 0.85, brightness * 0.95); // toque frío
      rect(b.x, baseY - b.h, b.w, b.h);
    }
  }


function cambiarEstado(nuevo) {
  if (!estados[nuevo]) return;

  let cfgNuevo = estados[nuevo];
  let ahora = millis();

  for (let b of skyline) {
    // 🟡 Primero: marcar ventanas existentes para apagarse
    for (let w of b.windows) {
      w.targetOn = false;
      w.transitionTime = ahora + random(1000, 4000);
      w.keepColor = true;   // 👈 marca que NO debe cambiar de color
    }

    // 🟢 Segundo: calcular cuántas ventanas nuevas necesitamos para el nuevo estado
    let cols = int(b.w / 6);
    let rows = int(b.h / 8);
    let totalVentanasDeseadas = cols * rows * cfgNuevo.densidadVentanas;

    let initialColor = cfgNuevo.colorLuz;

    while (b.windows.length < totalVentanasDeseadas) {
      let nx = b.x + 3 + int(random(cols)) * 6;
      let ny = height - b.h + 6 + int(random(rows)) * 8;
      b.windows.push({
        x: nx,
        y: ny,
        on: false,
        targetOn: true,
        turnOnTime: ahora + random(500, 5000), // 👈 encendido progresivo
        transitionTime: ahora + random(500, 5000),
        color: [initialColor[0], initialColor[1], initialColor[2]],
        targetColor: [initialColor[0], initialColor[1], initialColor[2]],
        brightness: random(120, 200),
        keepColor: false  // 👈 las nuevas sí tienen color del nuevo estado
      });
    }
  }

  currentState = nuevo;
  console.log("✨ Transición al estado:", nuevo);
}



function actualizarDensidadVentanas(densidad) {
  skyline.forEach(b => {
    b.windows = [];
    let cols = int(b.w / 6);
    let rows = int(b.h / 8);
    let startX = b.x + 3;
    let startY = height - b.h + 6;
    for (let i = 0; i < cols; i++) {
      for (let j = 0; j < rows; j++) {
        if (random() > densidad) {
          b.windows.push({
            x: startX + i * 6,
            y: startY + j * 8,
            on: true,
            brightness: random(120, 200)
          });
        }
      }
    }
  });
}

function keyPressed() {
  if (key === '1') cambiarEstado('intro');
  if (key === '2') cambiarEstado('verso1');
  if (key === '3') cambiarEstado('coro');
  if (key === '4') cambiarEstado('verso2');
  if (key === '5') cambiarEstado('puente');
  if (key === '6') cambiarEstado('amanecer');
  if (key === '7') cambiarEstado('outro');
}

function mousePressed() {
    if (!audioStarted) {
        userStartAudio();
        song.loop();
        audioStarted = true;
    }
}


```
**index.html**  
``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://cdn.jsdelivr.net/npm/p5@1.11.10/lib/p5.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/p5@1.11.10/lib/addons/p5.sound.min.js"></script>
    <script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
    <link rel="stylesheet" type="text/css" href="style.css">
    <meta charset="utf-8" />

  </head>
  <body>
    <main>
    </main>
    <script src="sketch.js"></script>
  </body>
</html>
```
### Mobile
**sketch.js** 
``` js
let socket;
let slider;

function setup() {
  noCanvas(); // 👈 Ya no necesitamos un canvas para botones
  socket = io();
  console.log('📡 Conectado desde mobile');

  // --- Slider para transición ---
  slider = createSlider(0, 100, 0);
  slider.position(20, windowHeight - 80);
  slider.style('width', windowWidth - 40 + 'px');

  slider.input(() => {
    const valor = slider.value();
    console.log('🌅 Slider:', valor);
    socket.emit('message', { type: 'slider', value: valor });
  });
}
```
**style.css**
``` css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');

* {
  box-sizing: border-box;
}

body {
  background: linear-gradient(135deg, #1a1a2e, #16213e);
  color: #fff;
  font-family: 'Inter', sans-serif;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  text-align: center;
  max-width: 400px;
  width: 90%;
}

h1 {
  margin-bottom: 1rem;
  font-size: 1.8rem;
}

.button-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
  margin-bottom: 1.5rem;
}

.state-btn {
  background: #0f3460;
  border: none;
  padding: 14px;
  font-size: 1rem;
  color: white;
  border-radius: 10px;
  transition: all 0.2s ease;
  cursor: pointer;
}

.slider-label {
  color: #fff;
  margin-top: 20px;
  font-size: 1rem;
  opacity: 0.8;
  text-align: center;
}


.state-btn:hover {
  background: #533483;
}

.state-btn.active {
  background: #e94560;
  box-shadow: 0 0 15px #e94560aa;
}

#status {
  font-size: 1rem;
  opacity: 0.8;
  padding: 8px;
  border-radius: 6px;
  display: inline-block;
  background: #444;
}

#status.connected {
  background: #1e8449;
}
```
**index.html**
``` html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Visualizer Control</title>
  <script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.11.0/lib/p5.min.js"></script>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="container">
    <h1>🎛 Control Visualizer</h1>

    <div class="button-grid">
      <button onclick="enviarEstado('intro')">🌃 Intro</button>
      <button onclick="enviarEstado('verso1')">🌀 Verso 1</button>
      <button onclick="enviarEstado('coro')">✨ Coro</button>
      <button onclick="enviarEstado('verso2')">🔥 Verso 2</button>
      <button onclick="enviarEstado('puente')">🌉 Puente</button>
      <button onclick="enviarEstado('amanecer')">🌄 Amanecer</button>
      <button onclick="enviarEstado('outro')">🌙 Outro</button>
    </div>

    <div class="slider-label">🌅 Transición noche → día</div>
    <!-- El slider real lo crea p5 en sketch.js -->

    <div id="status">📡 Desconectado</div>
  </div>

  <script>
    function enviarEstado(estado) {
      socket.emit('message', { type: 'cambiarEstado', value: estado });
      console.log('📤 Estado enviado:', estado);
    }
  </script>
  <script src="sketch.js"></script>
</body>
</html>
```

## Autoevaluación  
| Actividad  | Justificación  | Evidencia  |
|---|---|---|
| 1  | Completa, del todo  |   |
| 2  | Me faltó adjuntar las fotos, entonces quedó muy a medias  |   |
| 3  | Aquí me faltó responder una pregunta que, la verdad me dio pereza  |   |
| 4  | No la hice  |   |
| 5  | Completa, con mucha IA, pero hecha y entendida a fin de cuentas.  |   |


Nota final: Dado que me faltaron un par de puntos y una actividad completa, creo que me merezco un **3,8**, que al menos esta vez hice el apply.


