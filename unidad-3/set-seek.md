# Unidad 3

## 🔎 Fase: Set + Seek

### Actividad 05  
1. .
``` c++

from microbit import *
import utime
import music

display.clear()

class Evento:
    def __init__(self):
        self.valor = 0
    def set(self, nuevo):
        self.valor = nuevo
    def clear(self):
        self.valor = 0
    def leer(self):
        return self.valor

class TareaSerial:
    def __init__(self):
        uart.init(baudrate=115200)
    def update(self):
        if uart.any():
            dato = uart.read(1)
            if dato:
                if dato[0] == ord('A'):
                    evento.set('A')
                elif dato[0] == ord('B'):
                    evento.set('B')
                elif dato[0] == ord('S'):
                    evento.set('S')
                elif dato[0] == ord('T'):
                    evento.set('T')


class TareaBotones:
    def __init__(self):
        pass
    def update(self):
        if button_a.was_pressed():
            evento.set('A')
        elif button_b.was_pressed():
            evento.set('B')
        elif accelerometer.was_gesture('shake'):
            evento.set('S')
        elif pin_logo.is_touched():
            evento.set('T')


class TareaBomba:
    def __init__(self):
        self.CLAVE = ['A','B','A']
        self.intentos = [''] * len(self.CLAVE)
        self.posClave = 0
        self.tiempo = 20
        self.inicio = utime.ticks_ms()
        self.estado = 'CONFIG'
        display.clear()
        display.show(self.tiempo, wait=False)

    def update(self):
        if self.estado == 'CONFIG':
            if evento.leer() == 'A':
                evento.clear()
                self.tiempo = min(self.tiempo + 1, 60)
                display.show(self.tiempo, wait=False)
            if evento.leer() == 'B':
                evento.clear()
                self.tiempo = max(10, self.tiempo - 1)
                display.show(self.tiempo, wait=False)
            if evento.leer() == 'S':
                evento.clear()
                self.inicio = utime.ticks_ms()
                self.estado = 'ARMADA'

        elif self.estado == 'ARMADA':
            if utime.ticks_diff(utime.ticks_ms(), self.inicio) > 1000:
                self.inicio = utime.ticks_ms()
                self.tiempo -= 1
                display.show(self.tiempo, wait=False)
                if self.tiempo == 0:
                    music.play(music.FUNERAL)
                    display.show(Image.SKULL)
                    self.estado = 'BOOM'
            if evento.leer() == 'A':
                evento.clear()
                self.intentos[self.posClave] = 'A'
                self.posClave += 1
            if evento.leer() == 'B':
                evento.clear()
                self.intentos[self.posClave] = 'B'
                self.posClave += 1
            if self.posClave == len(self.intentos):
                correcta = True
                for i in range(len(self.intentos)):
                    if self.intentos[i] != self.CLAVE[i]:
                        correcta = False
                        break
                if correcta:
                    self.tiempo = 20
                    display.show(self.tiempo, wait=False)
                    self.posClave = 0
                    self.estado = 'CONFIG'
                else:
                    self.posClave = 0

        elif self.estado == 'BOOM':
            if evento.leer() == 'T':
                evento.clear()
                self.tiempo = 20
                display.show(self.tiempo, wait=False)
                self.inicio = utime.ticks_ms()
                self.estado = 'CONFIG'


tareaBomba = TareaBomba()
tareaSerial = TareaSerial()
tareaBotones = TareaBotones()
evento = Evento()

while True:
    tareaSerial.update()
    tareaBotones.update()
    tareaBomba.update()
```

2. 


