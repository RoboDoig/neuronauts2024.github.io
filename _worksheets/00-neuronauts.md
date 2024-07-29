---
layout: worksheet
title: Neuronauts Introduction
permalink: /tutorials/neuronauts.html
local: "true"
---

Getting Started
---------------

1. Open Bonsai on your laptop. Find a teacher if you can't find it or it doesn't open.
   
Video Acquisition
-----------------

Bonsai can be used to acquire and record data from many different devices. The exercises below will make you comfortable with the most common Bonsai data types. The first data type we will discuss is an image, which is represented as a 2D matrix of pixels. Each pixel represents either a brightness value in a grayscale image, or a BGR colour value in a colour image.

### **Exercise 1:** Saving a video

![Saving a video]({{ site.baseurl }}/assets/images/acquisition-video.svg)

* Insert a `CameraCapture` source.
* Insert a `VideoWriter` sink.
* Configure the `FileName` property of the `VideoWriter` operator with a file name ending in `.avi`.
* Run the workflow and check that it generates a valid video file.

### **Exercise 2:** Saving a grayscale video

![Saving a grayscale video]({{ site.baseurl }}/assets/images/acquisition-grayvideo.svg)

* Insert a `Grayscale` transform between `CameraCapture` and `VideoWriter`.
* Run the workflow. The output should now be a grayscale movie.
* Modify the workflow so that it records **simultaneously** a colour and a grayscale movie.

Arduino Acquisition
-------------------

In order to communicate and interact with an Arduino using Bonsai, you must program the microcontroller to send and receive binary data from the computer via the USB cable. Fortunately, the Arduino environment already comes with a standard implementation of an efficient binary protocol called **[Firmata](https://github.com/firmata/arduino)** which can be used for serial communication with external applications such as Bonsai. Your Arduino has been pre-configured with firmware that runs this protocol.

### **Exercise 6:** Saving analog data

![Saving analog data]({{ site.baseurl }}/assets/images/acquisition-analog.svg)

* Insert an `AnalogInput` source.
* Configure the `PortName` property to point to the correct serial port where the Arduino is connected.
* Run the workflow and visualize the output of the analog source. What do you see?
* **Optional:** Connect a sensor to the analog input pin, e.g. a potentiometer or a button.
* Insert a `CsvWriter` sink. This operator records input data into a text file.
* Configure the `FileName` property of the `CsvWriter` operator with a file name ending in `.csv`.
* Run the workflow, record some interesting signal, and then open the result text data file.

### **Exercise 7:** Control an LED

![Control an LED]({{ site.baseurl }}/assets/images/acquisition-led.svg)

* Insert a `Boolean` source.
* Insert a `DigitalOutput` sink.
* Set the `Pin` property of the `DigitalOutput` operator to 13.
* Configure the `PortName` property.
* Run the workflow and change the `Value` property of the `Boolean` operator.
* **Optional:** Use your mouse to control the LED! Replace the `Boolean` operator by a `MouseMove` source (hint: use `GreaterThan`, `LessThan`, or equivalent operators to connect one of the mouse axis to `DigitalOutput`).

### **Exercise 8:** Control a servo motor

![Control a servo motor]({{ site.baseurl }}/assets/images/acquisition-servo.svg)

* Insert a `Timer` source. Set its `Period` property to 500 ms.
* Insert a `Take` operator. Set its `Count` property to 10.
* Insert a `Rescale` operator. Set its `Max` property to 10, and its `RangeMax` property to 180.
* Insert a `Repeat` operator.
* Insert a `ServoOutput` sink.
* Set the `Pin` property of the `ServoOutput` operator to 9.
* Configure the `PortName` property.
* Connect a servo motor to the Arduino pin 9 and run the workflow. Can you explain the behaviour of the servo?
* **Optional:** Make the servo sweep back and forth.