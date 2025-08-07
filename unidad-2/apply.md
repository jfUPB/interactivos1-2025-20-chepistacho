# Unidad 2


## 🛠 Fase: Apply

### Actividad 05 🐧

### Actividad 06 🐧  

``` python
# Imports go at the top
from microbit import *
import utime
import music
 
STATE_INIT = 0
STATE_CONFIG = 1
STATE_ARMED= 2
STATE_BOOM = 3
 
start_time = 0
conteo = 0
current_state = STATE_CONFIG

while True:
    while current_state == STATE_CONFIG:
        conteo = 20
        display.show(conteo)
        if button_a.was_pressed():
            conteo += 1
            if conteo >= 60:
                conteo = 60
        if button_b.was_pressed():
            conteo -= 1
            if conteo <= 10:
                conteo = 10
     
        if accelerometer.was_gesture('shake'):
            current_state = STATE_ARMED
            start_time = utime.ticks_ms()
     
    while current_state == STATE_ARMED:
        display.show(conteo)
        if utime.ticks_diff(utime.ticks_ms(), start_time) > 1000:
            conteo -= 1
            start_time = utime.ticks_ms()
            if conteo <= 0:
                current_state = STATE_BOOM
    
    while current_state == STATE_BOOM:
        display.show(Image.SKULL)
        music.play(music.FUNERAL)
        if pin_logo.is_touched():
            current_state = STATE_CONFIG
```
