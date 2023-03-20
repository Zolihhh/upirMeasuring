# Feladat lépései
0. Betelepülés a Raspberry-be.
    - Projektek 2023/upirMeasuring

1. Kapcsoló külön külön kapcsolja be a ledeket egymás után.
    - 4 állapot: `status`
        - `i`: árammérő állapot (`piros`)
        - `u`: feszültségmérő (`zöld`)
        - `r`: ellenállásmérő (`kék`)
        - `p`: teljesítménymérő (`fehér`)

2. A mért értékek és érték(az állapottól függően) kiírása a képernyőre az állapottal együtt.

3. A mért érték a kijelzőn jelenjen meg.

4. Esetleg a kód átalakítása oop-vé.(nem kötelező)

# 1. Állapotok kapcsolása

```py
import RPi.GPIO as GPIO
import time
GPIO.setwarnings(False)
GPIO.setmode(GPIO.BCM)
GPIO.cleanup()

pinSwitch = 13 #in
GPIO.setup(pinSwitch,GPIO.IN)
pinRed = 12
pinGreen = 6
pinBlue = 4
pinWhite = 25
GPIO.setup(pinRed,GPIO.OUT)
GPIO.setup(pinGreen,GPIO.OUT)
GPIO.setup(pinBlue,GPIO.OUT)
GPIO.setup(pinWhite,GPIO.OUT)
print(f"4 Led kapcsolóval (oop). Kilép Ctrl-C")

T = 0.3
auto = False
state = "U"

def ledOnOff(red, green, blue, white):
    GPIO.output(pinRed, red)
    GPIO.output(pinGreen, green)
    GPIO.output(pinBlue, blue)
    GPIO.output(pinWhite, white)
    
def ledsOnOffByState():
    global state
    if state == "U":       
        ledOnOff(1,0,0,0)
    elif state == "I":
        ledOnOff(0,1,0,0)
    elif state == "R":
        ledOnOff(0,0,1,0)
    elif state == "P":
        ledOnOff(0,0,0,1)
    print(f"State: {state}")
        
#Eseménykezelő: lehúzásra indul
def stateChange(channel):
    global state
    if state == "U":
        state = "I"
    elif state == "I":
        state = "R"   
    elif state == "R":
        state = "P"
    elif state == "P":
        state = "U"   
    ledsOnOffByState()

ledsOnOffByState()
#Esemény összekapcsolása az eseménykezelővel
GPIO.add_event_detect(pinSwitch, GPIO.FALLING, callback=stateChange, bouncetime=200)

try:
    while True:
        time.sleep(T)
        if auto == True:
            LedsOnOff(1)

except KeyboardInterrupt:
    print("Pinek lekapcsolva")
    GPIO.cleanup()
```