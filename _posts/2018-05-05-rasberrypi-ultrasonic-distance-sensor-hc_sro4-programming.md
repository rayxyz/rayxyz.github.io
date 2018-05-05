---
layout: post
title: "Build a RaspberryPi straming server"
date: 2018-05-05 15:47 +0800
categories: tech
---

# RasberryPi pins
[![](/assets/images/2018-05-05_distance-sensor-programming/raspi-model3-pins.png)](/assets/images/2018-05-05_distance-sensor-programming/raspi-model3-pins.png)

# Pics of my distance sensing system
[![](/assets/images/2018-05-05_distance-sensor-programming/raspi-hcsro4-battery-01.jpg)](/assets/images/2018-05-05_distance-sensor-programming/raspi-hcsro4-battery-01.jpg)

[![](/assets/images/2018-05-05_distance-sensor-programming/raspi-hcsro4-battery-02.jpg)](/assets/images/2018-05-05_distance-sensor-programming/raspi-hcsro4-battery-02.jpg)

# Code & running screen-shots
[![](/assets/images/2018-05-05_distance-sensor-programming/rspi-hcro4-code.jpg)](/assets/images/2018-05-05_distance-sensor-programming/rspi-hcro4-code.jpg)

# Code
```
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BOARD)

TRIG = 12
ECHO = 16

GPIO.setup(TRIG, GPIO.OUT)
GPIO.setup(ECHO, GPIO.IN)

GPIO.output(TRIG, True)
time.sleep(0.0001)
GPIO.output(TRIG, False)

while GPIO.input(ECHO) == False:
    start = time.time()

while GPIO.input(ECHO) == True:
    end = time.time()

sig_time = end - start

#cm:
distance = (sig_time * (340 * 100)) / 2

print('Distance: {} cm'.format(distance))

GPIO.cleanup()
```