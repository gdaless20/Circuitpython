# Circuitpython

## Table of Contents
* [Table of Contents](#TableOfContents)
* [Hello_CircuitPython](#Hello_CircuitPython)
* [CircuitPython_Servo](#CircuitPython_Servo)
* [CircuitPython_Distance Servo](#CircuitPython_Distance_Servo)
* [NextAssignmentGoesHere](#NextAssignment)
---

## Hello_CircuitPython

### Description & Code

The goal was to get the neopixel on the metro express board to  clors.I looked up code on adafruit.com and changed it to fit the metro express board and the function i needed it to serve .

```python
# SPDX-FileCopyrightText: 2021 ladyada for Adafruit Industries
# SPDX-License-Identifier: MIT

import time
import board
import neopixel


# On CircuitPlayground Express, and boards with built in status NeoPixel -> board.NEOPIXEL
# Otherwise choose an open pin connected to the Data In of the NeoPixel strip, i.e. board.D1
pixel_pin = board.NEOPIXEL

# On a Raspberry pi, use this instead, not all pins are supported
# pixel_pin = board.D18

# The number of NeoPixels
num_pixels = 10

# The order of the pixel colors - RGB or GRB. Some NeoPixels have red and green reversed!
# For RGBW NeoPixels, simply change the ORDER to RGBW or GRBW.
ORDER = neopixel.GRB

pixels = neopixel.NeoPixel(
    pixel_pin, num_pixels, brightness=0.2, auto_write=False, pixel_order=ORDER
)


def wheel(pos):
    # Input a value 0 to 255 to get a color value.
    # The colours are a transition r - g - b - back to r.
    if pos < 0 or pos > 255:
        r = g = b = 0
    elif pos < 85:
        r = int(pos * 3)
        g = int(255 - pos * 3)
        b = 0
    elif pos < 170:
        pos -= 85
        r = int(255 - pos * 3)
        g = 0
        b = int(pos * 3)
    else:
        pos -= 170
        r = 0
        g = int(pos * 3)
        b = int(255 - pos * 3)
    return (r, g, b) if ORDER in (neopixel.RGB, neopixel.GRB) else (r, g, b, 0)


def rainbow_cycle(wait):
    for j in range(255):
        for i in range(num_pixels):
            pixel_index = (i * 256 // num_pixels) + j
            pixels[i] = wheel(pixel_index & 255)
        pixels.show()
        time.sleep(wait)


while True:
    # Comment this line out if you have RGBW/GRBW NeoPixels
    pixels.fill((255, 0, 0))
    # Uncomment this line if you have RGBW/GRBW NeoPixels
    # pixels.fill((255, 0, 0, 0))
    pixels.show()
    time.sleep(1)

    # Comment this line out if you have RGBW/GRBW NeoPixels
    pixels.fill((0, 255, 0))
    # Uncomment this line if you have RGBW/GRBW NeoPixels
    # pixels.fill((0, 255, 0, 0))
    pixels.show()
    time.sleep(1)

    # Comment this line out if you have RGBW/GRBW NeoPixels
    pixels.fill((0, 0, 255))
    # Uncomment this line if you have RGBW/GRBW NeoPixels
    # pixels.fill((0, 0, 255, 0))
    pixels.show()
    time.sleep(1)

    rainbow_cycle(0.001)  # rainbow cycle with 1ms delay per step
    
  


```
[Code link](https://learn.adafruit.com/adafruit-metro-m0-express-designed-for-circuitpython/circuitpython-internal-rgb-led)

### Evidence


https://user-images.githubusercontent.com/71349797/133492050-43797708-d633-4f63-bfa7-f6a821c85aad.mov



### Wiring
There was no wiring for this assignment .

### Reflection
I struggled a lot with making the nano pixel flash different colors . When i tried to write my own code from scratch it would flash red , yellow , and green then flash blue a few times. What ended up working for me was researching online for already written portions of code for this project . I was able to find something that worked and then was able to get my led to flash.



## CircuitPython_Servo

### Description & Code

With using Mu and python code to make a servo slowly turn .

```python
"""CircuitPython Essentials Servo continuous rotation servo example"""
import time
import board
import pwmio
from adafruit_motor import servo

# create a PWMOut object on Pin A2.
pwm = pwmio.PWMOut(board.A2, frequency=50)

# Create a servo object, my_servo.
my_servo = servo.ContinuousServo(pwm)

while True:
    print("forward")
    my_servo.throttle = 1.0
    time.sleep(2.0)
    print("stop")
    my_servo.throttle = 0.0
    time.sleep(2.0)
    print("reverse")
    my_servo.throttle = -1.0
    time.sleep(2.0)
    print("stop")
    my_servo.throttle = 0.0
    time.sleep(4.0)


```

### Evidence


https://user-images.githubusercontent.com/71349797/133495444-635f8a92-3d28-4106-9f73-40e3735322d4.MOV




### Wiring

![Start Simulating](https://user-images.githubusercontent.com/71349797/133495354-0ef9219e-2658-4c7b-bef3-01cff6986baf.png)

### Reflection
 I once again had a problem with my code when i tried doing it from scratch . Im not very good at coding so ive learned that researching what code im going to write before i do it helps me a lot in the long run and tends to save me time.



## CircuitPython_Distance_Servo

### Description & Code

With Mu i coded a distance servo to detect how close an object is and show a certain color that corresponds to the distance.


```python
import time
import board
import adafruit_hcsr04
import neopixel
import simpleio

sonar = adafruit_hcsr04.HCSR04(trigger_pin=board.D5, echo_pin=board.D6)
dot = neopixel.NeoPixel(board.NEOPIXEL, 10, brightness=0.5)

r = 0
g = 0
b = 0


while True:

    try:
        distance = sonar.distance
        print((distance,))

        if distance < 5:
            r = 255
            g = 0
            b = 0
        elif distance > 5 and distance < 20:
            r = simpleio.map_range(distance, 5, 20, 255, 0)
            b = simpleio.map_range(distance, 5, 20, 0, 255)
            g = 0
            r = int(r)
            g = int(g)
            b = int(b)
        elif distance > 20 and distance < 35:
            r = 0
            b = simpleio.map_range(distance, 20, 35, 255, 0)
            g = simpleio.map_range(distance, 20, 35, 0, 255)
            r = int(r)
            g = int(g)
            b = int(b)
        elif distance > 35:
            r = 0
            b = 0
            g = 255
            r = int(r)
            g = int(g)
            b = int(b)
        print(r, g, b)
        time.sleep(0.05)

    except RuntimeError:
        print("Retrying!")
        r = 0
        g = 0
        b = 255
        time.sleep(0.1)

    print(r, g, b)
    dot.fill((r, g, b))
    time.sleep(0.05)




```

### Evidence
![ezgif com-gif-maker](https://user-images.githubusercontent.com/71349797/134724959-4f1d69a2-bb28-4c98-8dd7-6bff58e07b80.gif)

### Wiring
![Powerful Esboo](https://user-images.githubusercontent.com/71349797/134725601-72db0fcb-0d50-486c-aff5-9e0ec1772057.png)

### Reflection
This project was very stressful as I could not get my serial monitor to show distance, I had to use my peers to help me and I was eventually able to figure it out.




## NextAssignment

### Description & Code

```python
from digitalio import DigitalInOut, Direction, Pull
import time
import board
initial = time.monotonic()
interrupter = DigitalInOut(board.D7)
interrupter.direction = Direction.INPUT
interrupter.pull = Pull.UP

counter = 0

photo = False
state = False

while True:
    photo = interrupter.value
    if photo and not state:
            counter += 1
    state = photo

    now = time.monotonic()
    if now - initial >= 4:
        print("Interrupts:", str(counter))
        initial = now 
    time.sleep(0.1)

```

### Evidence

### Wiring

### Reflection
