
# Evidencias de la unidad 8

## Actividad 01
La verdad, no quiero pensar tanto, entonces voy a usar el mismo visualizer de la unidad pasada y lo voy a modificar.  
Creo que la mejor adaptación del código sería quitar el slider del programa original del móvil y reemplazarlo por los botones del micro:bit 
<img width="946" height="579" alt="image" src="https://github.com/user-attachments/assets/c2d8deea-7f14-4205-b0f0-0b2e1c84ba19" />  
No rcuerdo si el bloque del **Devtunnels** y el del **Servidor** están en el órden que es, pero tal que así queda el diagrama.

## Actividad 02
Contra todo pronóstico, sí hice el apply:  
Al index del desktop le agregué la línea de la primera unidad, y el sketch corregido es el siguiente:  
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
// Serial / micro:bit
let serialPort = null;      // <-- antes habías puesto "port" y choca con const port=3000
let microbitReader = null;
let microbitConnected = false;




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
  
    // botón para conectar micro:bit (se muestra sobre el canvas)
    let connectButton = createButton("🔗 Conectar micro:bit");
    connectButton.position(width - 220, 20); // arriba a la derecha, ajustalo si querés
    connectButton.style("padding", "10px 14px");
    connectButton.style("font-size", "14px");
    connectButton.style("background", "#1e90ff");
    connectButton.style("color", "#fff");
    connectButton.style("border", "none");
    connectButton.style("border-radius", "6px");
    connectButton.style("cursor", "pointer");

    connectButton.mousePressed(async () => {
      try {
        serialPort = await navigator.serial.requestPort();
        await serialPort.open({ baudRate: 115200 });
        microbitReader = serialPort.readable.getReader();
        microbitConnected = true;
        console.log("🟢 Micro:bit conectado");
        connectButton.hide(); // desaparece al conectarse
        listenToMicrobit();
      } catch (err) {
        console.error("❌ Error al conectar micro:bit:", err);
      }
    });





    socket = io();

    socket.on('connect', () => {
        console.log('Connected to server');
    });

    socket.on('message', (msg) => {
        console.log('📩 Mensaje recibido:', msg);

        if (msg.type === 'cambiarEstado') {
            cambiarEstado(msg.value);
        } else {
            console.log('⚠️ Tipo de mensaje no reconocido:', msg.type);
        }
    });

    socket.on('disconnect', () => {
        console.log('Disconnected from server');
    });

    socket.on('connect_error', (error) => {
        console.error('Socket.IO error:', error);
    });

  fft = new p5.FFT();
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

async function listenToMicrobit() {
  const decoder = new TextDecoder();
  let buffer = "";

  while (microbitConnected && microbitReader) {
    try {
      const { value, done } = await microbitReader.read();
      if (done) break;
      if (!value) continue;

      buffer += decoder.decode(value, { stream: true });

      // procesamos líneas completas
      let lines = buffer.split(/\r?\n/);
      // dejamos el último fragmento en el buffer (puede ser parcial)
      buffer = lines.pop();

      for (let line of lines) {
        let data = line.trim();
        if (!data) continue;
        console.log("📥 microbit:", data);

        // Ajustes graduales: cambiamos transitionFactor en pasos de 0.05
        // transitionFactor usa 0..1 en tu visualizer, así que lo ajustamos así.
        if (data.includes("Button A")) {
          transitionFactor = constrain(transitionFactor - 0.05, 0, 1);
          console.log("⬇️ transitionFactor:", transitionFactor);
        } else if (data.includes("Button B")) {
          transitionFactor = constrain(transitionFactor + 0.05, 0, 1);
          console.log("⬆️ transitionFactor:", transitionFactor);
        }
      }

    } catch (err) {
      console.error("💀 Error leyendo micro:bit:", err);
      break;
    }
  }

  // limpieza cuando se sale del loop
  if (microbitReader) {
    try { await microbitReader.releaseLock(); } catch(e) {}
    microbitReader = null;
  }
}



function draw() {
  // --- fondo interpolado según transitionFactor ---
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

    // 🌠 Estrellas titilantes con brillo dependiente del estado y slider
    noStroke();
    blendMode(ADD);
    for (let s of stars) {
      let flicker = sin(frameCount * s.flickerSpeed + s.offset) *
                    map(trebleEnergy, 0, 255, 0.6, 2.5);
      let brightness = constrain(s.baseBrightness + flicker * 80, 100, 255);

      // Las estrellas se apagan según transitionFactor (0..1)
      brightness *= (1 - transitionFactor);

      fill(brightness);
      circle(s.x, s.y, s.size + random(0.5));
    }

    // edificios y elementos secundarios
    drawChrysler(midEnergy);
    drawOWTC(midEnergy);
    drawSecondarySkyline(bassEnergy);

    // 🏙️ Skyline principal
    blendMode(BLEND);
    noStroke();
    rectMode(CORNER);
    let baseY = height;
    for (let b of skyline) {
      fill(15); // cuerpo del edificio, opaco
      rect(b.x, baseY - b.h, b.w, b.h);

      // Empire State (secciones + glow)
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

        fill(25);
        for (let s of sections) {
          let y = top + s.offset - s.h / 2;
          rect(cx, y, s.w, s.h);
        }

        // glow dinámico por encima de las secciones (usa transitionFactor para suavizar)
        let glowIntensity = map(midEnergy, 0, 255, 30, 160) * (1 - transitionFactor*0.9);
        for (let s of sections) {
          let y = top + s.offset - s.h / 2;
          let glowHeight = s.h * 1.3;
          for (let g = 0; g < glowHeight; g += 2) {
            let alpha = map(g, 0, glowHeight, glowIntensity, 0);
            fill(cfg.colorLuz[0], cfg.colorLuz[1], cfg.colorLuz[2], alpha);
            rect(cx, y + s.h / 2 - g, s.w * 0.9, 2);
          }
        }

        // antena
        stroke(230 * (1 - transitionFactor*0.8));
        strokeWeight(1.2);
        let topSection = sections[sections.length - 1];
        let spireBase = top + topSection.offset - topSection.h;
        let spireTop = spireBase - 50;
        line(cx, spireBase, cx, spireTop);

        pop();
      }
    }

    // 💡 Ventanas encendiéndose y titilando (frontal)
    noStroke();
    let elapsed = millis();
    for (let b of skyline) {
      for (let w of b.windows) {
        if (!w.color || w.color.length !== 3) {
          let base = estados[currentState].colorLuz;
          w.color = [base[0], base[1], base[2]];
        }
        if (!w.targetColor || w.targetColor.length !== 3) {
          w.targetColor = [w.color[0], w.color[1], w.color[2]];
        }
        if (typeof w.targetOn === 'undefined') w.targetOn = true;
        if (typeof w.transitionTime === 'undefined') w.transitionTime = millis();
        if (typeof w.turnOnTime === 'undefined') w.turnOnTime = millis() + random(0, 2000);

        if (!w.on && elapsed > w.turnOnTime) w.on = true;
        if (w.targetOn && !w.on && millis() > w.transitionTime) w.on = true;
        if (!w.targetOn && w.on && millis() > w.transitionTime) w.on = false;

        if (!w.keepColor) {
          w.color[0] = lerp(w.color[0], w.targetColor[0], 0.05);
          w.color[1] = lerp(w.color[1], w.targetColor[1], 0.05);
          w.color[2] = lerp(w.color[2], w.targetColor[2], 0.05);
        }

        if (w.on) {
          let pulse = map(sin(frameCount * 0.05 + w.x * 0.01), -1, 1, 0.8, 1.2);
          let alpha = map(midEnergy, 0, 255, 60, 150) * pulse;

          // factor de fade controlado por transitionFactor (0..1)
          let factorFade = lerp(1, 0.08, transitionFactor);
          let fadedAlpha = alpha * factorFade;

          fill(constrain(w.color[0],0,255), constrain(w.color[1],0,255), constrain(w.color[2],0,255), fadedAlpha);
          rect(w.x, w.y, 3, 4);
        }
      }
      b.windows = b.windows.filter(w => w.on || w.targetOn);
    }
  } else {
    // si no empezó el audio, igual podés mostrar cielo interpolado y estrellas según slider
    noStroke();
    blendMode(ADD);
    for (let s of stars) {
      let brightness = s.baseBrightness * (1 - transitionFactor);
      fill(brightness);
      circle(s.x, s.y, s.size);
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

  // Queremos que el secondary skyline mantenga contraste aun cuando cambia el cielo.
  // Calculamos un brillo base que no sea demasiado dependiente del fondo.
  // b.depth está entre ~0.3 y 0.6; lo usamos para variar tamaño/tono.
  for (let b of secondarySkyline) {
    // brillo base independiente del fondo, pero modulable según transitionFactor
    // más día -> edificios ligeramente más claros (pero sin perder contraste)
    let baseShade = map(b.depth, 0.2, 0.6, 40, 90);
    // ajustar por transitionFactor: si se acerca al día, subimos un poco el tono
    let shade = lerp(baseShade, baseShade + 30, transitionFactor * 0.6);

    // pintar el edificio con alpha completo (no depender del fondo)
    fill(shade, shade * 0.95, shade * 0.9);
    rect(b.x, baseY - b.h, b.w, b.h);

    // ventanas “vivas” usando bajos (pero atenuadas por transitionFactor)
    let glow = map(bassEnergy, 0, 255, 30, 180);
    for (let w of b.windows) {
      let pulse = map(sin(frameCount * 0.05 + w.x * 0.01), -1, 1, 0.7, 1.3);
      let alpha = glow * pulse;

      // apagar según transitionFactor
      alpha *= (1 - transitionFactor);

      // mosrar ventanas con el color actual del estado
      let cfg = estados[currentState];
      fill(cfg.colorLuz[0], cfg.colorLuz[1], cfg.colorLuz[2], alpha);
      rect(w.x, w.y, 3, 4);
    }
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
Lo único que cambia con respecto a la de la pasada es que le agruegué un par de variables, y una función para modificar el `tranitionFactor` (el responsable de la transición del amanecer) por medio del micro:bit. El resto está completamente igual.


