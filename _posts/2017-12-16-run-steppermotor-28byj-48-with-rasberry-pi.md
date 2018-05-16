---
layout: post
title: "Working with setp motor"
date: 2017-12-16 20:43 +0800
categories: tech
---

# Running step motor
[![](/assets/images/robotics/stepmotor/running-stpmt.jpg)](/assets/images/robotics/stepmotor/running-stpmt.jpg)

# Code
{% highlight python %}
```
#!/usr/bin/env python
import RPi.GPIO as GPIO
import time

IN1 = 7
IN2 = 13
IN3 = 15
IN4 = 18

def setStep(w1, w2, w3, w4):
    GPIO.output(IN1, w1)
    GPIO.output(IN2, w2)
    GPIO.output(IN3, w3)
    GPIO.output(IN4, w4)

def stop():
    setStep(0, 0, 0, 0)

def forward(delay, steps):
    for i in range(0, steps):
        setStep(1, 0, 0, 0)
        time.sleep(delay)
        setStep(0, 1, 0, 0)
        time.sleep(delay)
        setStep(0, 0, 1, 0)
        time.sleep(delay)
        setStep(0, 0, 0, 1)
        time.sleep(delay)

def backward(delay, steps):
    for i in range(0, steps):
        setStep(0, 0, 0, 1)
        time.sleep(delay)
        setStep(0, 0, 1, 0)
        time.sleep(delay)
        setStep(0, 1, 0, 0)
        time.sleep(delay)
        setStep(1, 0, 0, 0)
        time.sleep(delay)

def setup():
    print("In setup....")
    GPIO.setwarnings(False)
    print("set mode")
    GPIO.setmode(GPIO.BOARD)       # Numbers GPIOs by physical location
    print("set up in1")
    GPIO.setup(IN1, GPIO.OUT)      # Set pin's mode is output
    print("set up in2")
    GPIO.setup(IN2, GPIO.OUT)
    print("set up in3")
    GPIO.setup(IN3, GPIO.OUT)
    print("set up in4")
    GPIO.setup(IN4, GPIO.OUT)

def loop():
    while True:
        print "backward..."
        backward(0.003, 512)  # 512 steps --- 360 angle

        print "stop..."
        stop()                 # stop
        time.sleep(3)          # sleep 3s

        print "forward..."
        forward(0.005, 512)

        print "stop..."
        stop()
        time.sleep(3)

def destroy():
    GPIO.cleanup()             # Release resource

if __name__ == '__main__':     # Program start from here
    setup()
    try:
        loop()
    except KeyboardInterrupt:  # When 'Ctrl+C' is pressed, the child function destroy() will be  executed.
        destroy()
```
{% endhighlight %}