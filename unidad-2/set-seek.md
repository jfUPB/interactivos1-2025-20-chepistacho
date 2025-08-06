# Unidad 2

## 🔎 Fase: Set + Seek

### Actividad 01  🐧
``` python
from microbit import *
import utime

class Pixel:
    def __init__(self,pixelX,pixelY,initState,interval):
        self.state = "Init"
        self.startTime = 0
        self.interval = interval
        self.pixelX = pixelX
        self.pixelY = pixelY
        self.pixelState = initState

    def update(self):

        if self.state == "Init":
            self.startTime = utime.ticks_ms()
            self.state = "WaitTimeout"
            display.set_pixel(self.pixelX,self.pixelY,self.pixelState)

        elif self.state == "WaitTimeout":
            if utime.ticks_diff(utime.ticks_ms(),self.startTime) > self.interval:
                self.startTime = utime.ticks_ms()
                if self.pixelState == 9:
                    self.pixelState = 0
                else:
                    self.pixelState = 9
                display.set_pixel(self.pixelX,self.pixelY,self.pixelState)

pixel1 = Pixel(0,0,0,1000)
pixel2 = Pixel(4,4,0,500)

while True:
    pixel1.update()
    pixel2.update()
```
1. Este código define una clase `Pixel`, y crea dos objetos de esta clase. Cada uno de estos representa un pixel del micro:bit con su respectiva coordenada, intensidad e intervalo en el cual va a estar encendido.
2. Está el pseudo-estado **Init** (como en todos los códigos que hamos visto hasta ahora en c++ y Python), y el estado **WaitTimeout**.
3. El único evento que pude identificar en este código es el paso del tiempo, el cual es el evento que saca al código del estado **WaitTimeout**.
4. Las acciones del programa podrían ser el cambio de estado de los LEDs (prendiddo o apagado), en función del paso del tiempo.

### Actividad 02 🐧  

Código del semáforo:  
``` python
# Imports go at the top
from microbit import *

estado = "verde"

# Code in a 'while True:' loop repeats forever
while True:
    if estado == "verde":
        display.clear()
        display.set_pixel(2, 4, 9)
        sleep(3000)
        estado = "amarillo"  
    elif estado == "rojo":
        display.clear()
        display.set_pixel(2, 0, 9)
        sleep(3000)
        estado = "verde"
    elif estado == "amarillo":  
        display.clear()
        display.set_pixel(2, 2, 9)
        sleep(1000)
        estado = "rojo"
```
En este código, se pueden observar los estados **Rojo**, **Amarillo** y **Verde**, correspondientes a los tres estados que tiene un semáforo.  
Los eventos que esperan estos estados son simplemente el paso del tiempo, que hace que las luces brillen en su posición durante solo un momento, antes de cambiar al siguiente estado.  
Las acciones en este código son los cambios de estado en tres de los LEDs del micro:bit, que alumbrarán o se apagarán dependiendo del estado en el que el programa se encuentre.  

### Actividad 03 🐧
Código (El que hicimos en clase no me funcionó, entonces me baso en el de la página del curso): 
``` python
from microbit import *
import utime

STATE_INIT = 0
STATE_HAPPY = 1
STATE_SMILE = 2
STATE_SAD = 3

HAPPY_INTERVAL = 1500
SMILE_INTERVAL = 1000
SAD_INTERVAL = 2000

current_state = STATE_INIT
start_time = 0
interval = 0

while True:
    # pseudoestado STATE_INIT
    if current_state == STATE_INIT:
        display.show(Image.HAPPY)
        start_time = utime.ticks_ms()
        interval = HAPPY_INTERVAL
        current_state = STATE_HAPPY
    elif current_state == STATE_HAPPY:
        if button_a.was_pressed():
            # Acciones para el evento
            display.show(Image.SAD)
            # Acciones de entrada para el siguiente estado
            start_time = utime.ticks_ms()
            interval = SAD_INTERVAL
            current_state = STATE_SAD
        if utime.ticks_diff(utime.ticks_ms(), start_time) > interval:
            # Acciones para el evento
            display.show(Image.SMILE)
            # Acciones de entrada para el siguiente estado
            start_time = utime.ticks_ms()
            interval = SMILE_INTERVAL
            current_state = STATE_SMILE
    elif current_state == STATE_SMILE:
        if button_a.was_pressed():
            display.show(Image.HAPPY)
            start_time = utime.ticks_ms()
            interval = HAPPY_INTERVAL
            current_state = STATE_HAPPY
        if utime.ticks_diff(utime.ticks_ms(), start_time) > interval:
            display.show(Image.SAD)
            start_time = utime.ticks_ms()
            interval = SAD_INTERVAL
           current_state = STATE_SAD
    elif current_state == STATE_SAD:
        if button_a.was_pressed():
            display.show(Image.SMILE)
            start_time = utime.ticks_ms()
            interval = SMILE_INTERVAL
            current_state = STATE_SMILE
        if utime.ticks_diff(utime.ticks_ms(), start_time) > interval:
            display.show(Image.HAPPY)
            start_time = utime.ticks_ms()
            interval = HAPPY_INTERVAL
            current_state = STATE_HAPPY
```
1. El programa simula tareas concurrentes porque es capaz de manejar **simultáneamente** los eventos (el tiempo, en este caso), y las acciones al cambiar su estado, permitiendo respuestas del programa **sin detenerse**.
2. - **Estados**: Happy, Smile y Sad.
   - **Eventos**: Paso del tiempo, presionar el boton **A**.
   - **Acciones**: Mostrar un dibujo distinto dependiendo del estado en el que se encuentra el programa.
3. **Vectores de prueba**:  
       - El estado `Init` pasa directo al estado `Happy`, y muestra el dibujo correspondiente. ✔️  
       - En el estado `Happy`, cuando se presiona el botón **A**, pasa al estado `Sad` y muestra el dibujo correspondiente. ✔️  
       - En el estado `Smile`, si no se presiona el botón **A**, el programa dejará que pase el tiempo, y cambiará su estado y el dibujo a `Sad`. ✔️  


