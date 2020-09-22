# SparkFun Edge - Person detection with bounding boxes on the edge

This project aims at deploying a person detection model on a SparkFun Edge board.
Via the camera included on the board, persons in a meeting room should be counted
and their distance to each other can be measured.
With this data we can determine if a room is empty or filled, if people comply to the
corona restrictions and more.

Visit the [Wiki](https://iteragit.iteratec.de/sparkfun/sparky-rooms/-/wikis/home) for more information.


## Hardware Setup

Needed hardware:
- Board - [SparkFun Edge](https://www.sparkfun.com/products/15170)
- USB Serial Breakout - [SparkFun Serial Basic Breakout - CH340C and USB-C](https://www.sparkfun.com/products/15096)
- Camera - [Himax HM01B](https://www.sparkfun.com/products/15570)
- USB-C to USB cable


## Software Setup


**1. Install Arduino**

[Download the application from the Arduino website](https://www.arduino.cc/en/Main/Software).


**2. Install the TensorFlowLite Library**

The examples utilize the TensorFlowLight Arduino libary, which is installed using the Arduino Library Manager. 

To install this library, use the following steps:
* In Arduino, select the "Manage Libraries..." menu item. ***Tools > Manage Libraries...***
* In the Library Manager interface, search for tensorflow
* Select the library **Arduino_TensorFlowLite** by **TensorFlow Authors**
* Select the *non-precompiled* version of the libary. Version should match the version your model was trained with.
* Select the **Install** button to install the library

**3. Install the Himax HM01B0 Camera Library**

With the Library Manager dialog still displayed, install the Himax camera driver.
* Search for ***himax***
* Select the **SparkFun Himax HM01B0 Camera"** Library (tested on version 0.0.3) and then select the **Install** button.  

**4. Install the SparkFun Boards Package**

Load the SparkFun Boards package into the Arduino Board Manger.

To install package, use the following steps:

* In Arduino, open the Preferences menu item. ***File > Preferences***, (macOS) ***Arduino > Preferences***
* Add the following path to the ***Additional Boards Manager URLs:*** path in preferences.

  ```https://raw.githubusercontent.com/sparkfun/Arduino_Boards/master/IDE_Board_Manager/package_sparkfun_index.json```

* Select the ***OK*** button to save the preferences.

**5. Install the SparkFun Artemis Boards Package**

Once the location of the SparkFun boards package is set in the preferences, the board definition package for the SparkFun Apollo3 boards must be installed. 

To install package, use the following steps:
* In Arduino, open the Preferences menu item. ***Tools > Board "..." > Manage Boards...***
* Search for *SparkFun*
* Select the **SparkFun Apollo3 Boards" package (use v1.0.24 or later)
* Close the dialog
* Select the SparkFun Edge Board. ***Tools > Boards "..." > SparkFun Edge***
* Set the Bootloader to the ***SparkFun Variable Loader***. ***Tools > Bootloader "..." > SparkFun Variable Loader..."
