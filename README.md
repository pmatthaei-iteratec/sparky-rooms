# SparkFun Edge - Person detection with bounding boxes on the edge

Visit the [Wiki](https://iteragit.iteratec.de/sparkfun/sparky-rooms/-/wikis/home) for more information.


## Hardware Setup

- Board - [SparkFun Edge](https://www.sparkfun.com/products/15170)
- USB Serial Breakout - [SparkFun Serial Basic Breakout - CH340C and USB-C](https://www.sparkfun.com/products/15096)
- Camera - [Himax HM01B](https://www.sparkfun.com/products/15570)
- USB-C to USB cable


## Software Setup
--------------

### Install Arduino

[Download the application from the Arduino website](https://www.arduino.cc/en/Main/Software).

### Install the TensorFlowLite Library

The examples utilize the TensorFlowLight Arduino libary, which is installed using the Arduino Library Manager. 

To install this library, use the following steps:
* In Arduino, select the "Manage Libraries..." menu item. ***Tools > Manage Libraries...***
* In the Library Manager interface, search for tensorflow
* Select the library **Arduino_TensorFlowLite** by **TensorFlow Authors**
* Select the *non-precompiled* version of the libary. Version should match the version your model was trained with.
* Select the **Install** button to install the library

### Install the Himax HM01B0 Camera Library

With the Library Manager dialog still displayed, install the Himax camera driver.
* Search for ***himax***
* Select the **SparkFun Himax HM01B0 Camera"** Library (tested on version 0.0.3) and then select the **Install** button.  

Once the install is completed, close the Arduino *Library Manager* dialog.

### Install the SparkFun Boards Package

Load the SparkFun Boards package into the Arduino Board Manger.

To install package, use the following steps:

* In Arduino, open the Preferences menu item. ***File > Preferences***, (macOS) ***Arduino > Preferences***
* Add the following path to the ***Additional Boards Manager URLs:*** path in preferences.

  ```https://raw.githubusercontent.com/sparkfun/Arduino_Boards/master/IDE_Board_Manager/package_sparkfun_index.json```

* Select the ***OK*** button to save the preferences.

#### Install the SparkFun Artemis Boards Package 

Once the location of the SparkFun boards package is set in the preferences, the board definition package for the SparkFun Apollo3 boards must be installed. 

To install package, use the following steps:
* In Arduino, open the Preferences menu item. ***Tools > Board "..." > Manage Boards...***
* Search for *SparkFun*
* Select the **SparkFun Apollo3 Boards" package (use v1.0.24 or later)
* Close the dialog
* Select the SparkFun Edge Board. ***Tools > Boards "..." > SparkFun Edge***
* Set the Bootloader to the ***SparkFun Variable Loader***. ***Tools > Bootloader "..." > SparkFun Variable Loader..."

Running an Example
------------------

### Configure the Hardware
* If this is a camera example, make sure the camera is installed on the Edge board.
* Plug USB cable into computer
* Connect Serial to USB adapter to USB cable
* Plug in Edge board to adpater


### Configure the Software
* Install and setup Arduino as described [above](#software-setup).
* Download (or clone) this repo to favorite location
* Open  ```stop-motion.ino``` file in Arduino
* Select the ```SparkFun Edge``` board (Tools->Board under 'SparkFun Apollo3')
* Select the proper port to connect with the baord (Tools->Port)
  * Windows - COM Port
  * Linux - /dev/ttyUSB*, where * is a number. NOTE: Permissions on this device needs to include user read and write (chmod 666)
  * macOS - /dev/cu.usbserial* 
* Change the **Bootloader** from *Ambiq Secure Bootloader (Default)* to *SparkFun Variable Loader (Enable w/ Artemis Bootloader)* (Tools->Bootloader)
  * Note: If SVL loader does not work. Either burn the bootloader yourself or use Ambiq. (tested with Ambiq Secure Bootloader)
  * (Your boards have received the upgraded bootloader already)
* Change the ```SVL Baud Rate``` from *921600* to *460800*

### Upload
* Compile with the ```Verify``` button (checkmark symbol)
* Upload with the ```Upload``` button (arrow symbol)
  * If uploading fails try lowering the bootloader baud rate
* Hold 14 button, then press upload, release button when "Upload complete!" is printed out. (tested - there might be other ways to time upload.)

### Serial Monitor
* Open the Serial Monitor by clicking on the magnifying glass icon (top right) or (Tools->Serial Monitor)

