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

Video Tracking
--------------

Bonsai allows processing captured raw video data to extract real-time measures of behaviour or other derived quantities. The exercises below will introduce you to some of its online video processing capabilities.

### **Exercise 9:** Segmentation of a coloured object

![Segmentation of a coloured object]({{ site.baseurl }}/assets/images/acquisition-segmentation1.svg)

* Insert a `CameraCapture` source.
* Insert a `RangeThreshold` transform.
* Open the visualizer for the `RangeThreshold` operator.
* Configure the `Lower` and `Upper` properties of the `RangeThreshold` to isolate your coloured object (hint: click the small arrow to the left of each property to expand their individual values).

This method segments coloured objects by setting boundaries directly on the BGR colour space. This colour space is considered a poor choice for colour segmentation. Can you see why?

![Segmentation of a coloured object]({{ site.baseurl }}/assets/images/acquisition-segmentation2.svg)

* Replace the `RangeThreshold` operator by a `ConvertColor` transform. This node converts the image from the BGR colour space to the [Hue-Saturation-Value (HSV) colour space](https://en.wikipedia.org/wiki/HSL_and_HSV).
* Insert an `HsvThreshold` transform.
* Configure the `Lower`and `Upper` properties of the `HsvThreshold` to isolate the object.
* Test the resulting tracking under different illumination conditions.

### **Exercise 10:** Real-time position tracking

![Real-time position tracking]({{ site.baseurl }}/assets/images/acquisition-tracking.svg)

* Starting with the workflow from the previous exercise, insert a `FindContours` transform. This operator traces the contours of all the objects in a black-and-white image. An *object* is defined as a region of connected white pixels.
* Insert a `BinaryRegionAnalysis` transform. This node calculates the area, center of mass, and orientation for all the detected contours.
* Insert a `LargestBinaryRegion` transform to extract the largest detected object in the image.
* Select the `ConnectedComponent` > `Centroid` field of the largest binary region using the context menu.
* Record the position of the centroid using a `CsvWriter` sink.
* **Optional:** Open the CSV file in Excel/Python/MATLAB/R and plot the trajectory of the object.

### **Exercise 11:** Background subtraction and motion segmentation

![Background subtraction]({{ site.baseurl }}/assets/images/acquisition-backsubtraction.svg)

* Create a grayscale video workflow similar to *Exercise 2*.
* Insert a `Skip` operator. Set its `Count` property to 1.
* In a new branch, insert a `Take` operator. Set its `Count` property to 1.
* Combine the images from both branches using the `CombineLatest` combinator.
* Insert the `AbsoluteDifference` transform after `CombineLatest`.
* Insert a `Threshold` transform. Visualize the node output and adjust the `ThresholdValue` property.

*Describe in your own words what the above workflow is doing.*

![Motion segmentation]({{ site.baseurl }}/assets/images/acquisition-motionsegmentation.svg)

* Replace the `CombineLatest` operator with the `Zip` combinator.
* Delete the `Take` operator.

*Describe in your own words what the above modified workflow is doing.*

### **Exercise 12:** Measuring motion

![Measuring motion]({{ site.baseurl }}/assets/images/acquisition-motion.svg)

* Create a grayscale video stream similar to *Exercise 2*.
* Insert a `BackgroundSubtraction` transform. Set its `AdaptationRate` property to 1.
* Insert a `Sum` operator. This operator will sum the values of all the pixels in the image.
* Run the workflow, point the camera at a moving object and visualize the output of the `Sum` operator. Compare small movements to big movements. What happens to the signal when the object holds perfect still?
* Right-click the `Sum` operator. Select the `Scalar` > `Val0` member from the context menu.

**Note:** The `Sum` operator sums the pixel values across all image colour channels. However, in the case of grayscale binary images, there is only one active channel and its sum is stored in the `Val0` field.
{: .notice--info}

* Record the motion of an object using a `CsvWriter` sink.

Reaction Time
-------------

For this and subsequent exercises, we will use a simple reaction time task as our model systems neuroscience experiment. In this task, the subject needs to press a button as fast as possible following a stimulus, as described in the following diagram:

<span style="display:block;text-align:center">
![State Machine Diagram]({{ site.baseurl }}/assets/images/reactiontime.svg)
</span>

The task begins with an inter-trial interval (`ITI`), followed by stimulus presentation (`ON`). After stimulus onset, advancement to the next state can happen only when the subject presses the button (`success`) or a timeout elapses (`miss`). Depending on which event is triggered first, the task advances either to the `Reward` state, or `Fail` state. At the end, the task goes back to the beginning of the ITI state for the next trial.

### **Exercise 13:** Generating a fixed-interval stimulus

In this first exercise, you will assemble the basic hardware and software components required to implement the reaction time task. The wiring diagram below illustrates the hardware assembly. You can wire the LED into any digital input pin, but make sure to note the pin number for the steps below. Note that in the wiring diagram we are using the standard cable to connect the LED to an analog port, while also connecting the digital line of the LED to a digital output with a jumper wire.

![Reaction Time Circuit]({{ site.baseurl }}/assets/images/reaction-time-circuit.png){:height="300px"}

We will start by using a fixed-interval blinking LED as our stimulus.

![Create Arduino]({{ site.baseurl }}/assets/images/create-arduino.svg)

* To configure the Arduino analog sampling rate, insert a `CreateArduino` source.
* Configure the `PortName` to the Arduino port where the microcontroller is connected.
* Configure the `SamplingInterval` property to 10 ms.

![Reaction Time Stimulus]({{ site.baseurl }}/assets/images/reaction-time-stimulus.svg)

* Insert a `Timer` source and set its `DueTime` property to 1 second.
* Insert a `Boolean` source and set its `Value` property to `True`.
* Insert a `DigitalOutput` sink and set its `Pin` property to the Arduino pin where the LED is connected.
* Configure the `PortName` to the Arduino port where the microcontroller is connected.
* Insert a `Delay` operator and set its `DueTime` property to 200 milliseconds.
* Insert a `Boolean` source and set its `Value` property to `False`.
* Insert a `DigitalOutput` sink configured to the same `Pin` and `PortName`.
* Insert a `Repeat` operator.

### **Exercise 14:** Measuring reaction time

![Reaction Time Measurement]({{ site.baseurl }}/assets/images/reaction-time-measurement.svg)

* Insert an `AnalogInput` source.
* Set the `Pin` property to the analog pin number where the duplicate LED wire is connected.
* Insert a second `AnalogInput` source.
* Set the `Pin` property to the analog pin number where the button is connected.
* Connect both inputs to a `Zip` operator.
* Insert a `CsvWriter` sink and configure the `FileName` property.
* Insert a `RollingGraph` visualizer and set its `Capacity` property to 1000.
* Run the workflow, and verify that both the stimulus and the button are correctly recorded.

### **Exercise 15:** Synchronizing video with a visual stimulus

To analyze movement dynamics in the reaction time task, you will need to align individual frame timing to stimulus onset. To do this, you can take advantage of the fact that our simple visual stimulus can be seen in the camera image and recorded together with the behaviour.

![Synching LED]({{ site.baseurl }}/assets/images/synching-led.svg)

* Starting from the workflow in the previous exercise, insert a `CameraCapture` source and position the camera such that you can see clearly both the LED and the computer keyboard.
* Insert a `VideoWriter` sink and configure the `FileName` with a path ending in `.avi`.
* Insert a `Crop` transform and set the `RegionOfInterest` property to a small area around the LED.
* Insert a `Grayscale` transform.
* Insert a `Sum (Dsp)` transform. This operator will sum the brightness values of all the pixels in the input image.
* Select the `Scalar` > `Val0` field from the right-click context menu.
* Record the output in a text file using a `CsvWriter` sink.
* Open both the text file containing the Arduino data, and the text file containing video data, and verify that you have detected an equal number of stimulus in both files. What can you conclude from these two pieces of data?
* **Optional:** Open the raw video file and find the exact frame where the stimulus came on. If you compare different trials you might notice that the brightness of the LED in that first frame across two different trials is different. Why is that?

### **Exercise 16:** Trigger a visual stimulus using a button

To make our task more interesting, we will now trigger the stimulus manually using a key press and learn more about `SelectMany` along the way!

![Triggered Stimulus Outer]({{ site.baseurl }}/assets/images/triggered-stimulus-outer.svg)

* Insert a `KeyDown` source and set its `Filter` property to a keyboard key of choice.
* Insert a `SelectMany` operator and move the stimulus generation logic inside the nested node:

![Triggered Stimulus Inner]({{ site.baseurl }}/assets/images/triggered-stimulus-inner.svg)

*Why do we need to remove the `Repeat` operator?*

* Ask a friend to test your reaction time!
* **Optional:** In the current workflow, what happens if you press the stimulus key twice in succession? Can you fix the current behaviour by using one of the higher order operators?

### **Exercise 17:** Recording response-triggered videos

![Triggered Video Outer]({{ site.baseurl }}/assets/images/triggered-video-outer.svg)

* Starting from the previous workflow, insert another `AnalogInput` source with the `Pin` property set to the button press pin number.
* Insert a `GreaterThan` operator.
* Insert a `DistinctUntilChanged` operator.
* Insert a `Condition` operator.
* In a new branch coming off the `VideoWriter`, insert a `Delay` operator.
* Set the `DueTime` property of the `Delay` operator to 1 second.
* Insert a `WindowTrigger` operator, and set its `Count` property to 100.
* Insert a `SelectMany` operator and inside the nested node create the below workflow:

![Triggered Video Inner]({{ site.baseurl }}/assets/images/triggered-video-inner.svg)

* Run the workflow and record a few videos triggered on the button press.
* Inspect the videos frame by frame and check whether the response LED comes ON at exactly the same frame number across different trials.
* If it does not, why would this happen? And how would you fix it?