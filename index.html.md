---
title: Mudra SDK 

language_tabs: # must be one of https://git.io/vQNgJ
  - java: Java
  - cpp: C++
  - csharp: C#
  - fsharp: Unity

toc_footers:
  - <a href='https://www.getmudra.com/'>Mudra Inspire</a>
  - <a href='http://wearabledevices.co.il/'>Wearable Devices</a>

includes:
  - errors

search: true
---

# Getting Started
```java
//Please scroll below for code examples
```

```cpp
//Please scroll below for code examples
```

```csharp
//Please scroll below for code examples
```

```fsharp
//Please scroll below for code examples
```
## Welcome 

Welcome to the Mudra SDK! Use this documentation to create your own unique experiences based on the **Mudra Inspire &trade;** device and Mudra API. This page is where you can get all the information you need to develop your unique experience.
 
Our API includes language bindings for *Java (Android)*, *C# (Windows, Unity)* and *c++ (Windows)* with *Swift* for iOS coming soon. In order to use Mudra in your application, all you need is to register a callback. First, follow the methods below and use auto-connect to instantiate Mudra. Use the methods that begin with *set* to register the callback you need. Afterwards, create the callback function in your code as detailed below. 

## Device Modes

Mode | Description | LED Indication
--------- | ------- | -----------
Pairing | Flash green continuously | ![alt text](images/LED-behaviour_flashing-green_1.png)
Connected | Flash green for 1 second | ![alt text](images/LED-behaviour_green_on.png)
Low Battery | Quick burst flashing red | ![alt text](images/LED-behaviour_flashing-Red_1.png)
Charging | Flash blue continuously | ![alt text](images/LED-behaviour_blue_on.png)

### Android\iOS
Bluetooth should be turned on, if it is off then the Mudra Inspire application will prompt the user to turn it on.

### Windows
Insert a dedicated Bluetooth Receiver into the USB slot. Please use the following model: <a href='https://www.nordicsemi.com/?sc_itemid=%7BCDCCA013-FE4C-4655-B20C-1557AB6568C9%7D'>nRF52840 Dongle</a>. See instructions below for installation and usage. Please install <a href='https://www.nordicsemi.com/?sc_itemid=%7BB935528E-8BFA-42D9-8BB5-83E2A5E1FF5C%7D'>nRF connect</a> , which will automatically install usb ble drivers.

<aside class="success">
Remember — Your Mudra device should always be connected. Check your host device to verify.
</aside>

# API functions

## Access the API

### Android 

* To access the API you must first generate an access token                                                                                                
* Navigate to <a href='https://jitpack.io/'>https://jitpack.io/</a> and Sign in using your GitHub account and credentials                                                                  
* Authorize Jitpack in order to connect to Github                                                                                                                 
* Tap your username on the top left corner of the screen
* Copy the access token shown on the first line and add it to your project **gradle.properties** file:                                                                                                

```java
// gradle.properties
authToken='<Your Jitpack Token>'
```

* Add the repository to your project build.gradle file:

```java
// Project build.gradle
allprojects {
    repositories {
      		  
		google()
		jcenter()
		maven {
			    url 'https://jitpack.io'
			    credentials { username authToken }
		}
	}
}

// Java 8 support
compileOptions {
    sourceCompatibility 1.8
    targetCompatibility 1.8
}
```

```java
// App build.gradle file:
implementation 'com.github.wearable-devices:MudraAndroidSDK:+'

// Bluetooth support
implementation 'no.nordicsemi.android:ble:2.1.1'
implementation 'no.nordicsemi.android.support.v18:scanner:1.4.0'

// Android manifest file:
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
### Windows

#### Installation

* To access the API run <a href='https://drive.google.com/open?id=1dG1Ljlqn_AYVrB0on6ALeMAKKQpTqkif'>MudraSetup.msi</a> (private link, please contact for access)
  * Choose installation path
* Edit Environment variables
  * Add new environment variable named “Mudra” with value as the installation path
  * Add the installation path to the “Path” System variable
  * Restart your PC for the paths to take effect

#### C++ Usage
* Project properties
   * Under C/C++ / General / Additional Include Directories add $(Mudra)\Include\
   * Under Linker / General / Additional Library Directories add $(Mudra)\Lib\
   * Under Linker / Input add MudraSDK.lib
* Set your calibration
  * Follow the onboarding instructions in the Android Mudra Inspire application
  * Copy the calibration file from ANDROID_STORAGE_ROOT\Users\(left,right) path into your project (note this file is user and hand dependent)
  * Note-Use mudra.SetCalibration method, future updates will feature an online method with no file needed

### Unity

When developing for Unity over Windows, please see installation instructions for Windows above. Following installation, see folder *UnityPluginPackage* in the installation directory. Simply drag and drop this package into the Unity editor. The package includes a plugin for interfacing with Mudra directly from c#, using Mono, and an example scene.
Please make sure you are using the latest version of Unity and that the dongle is plugged in to your PC.

```cpp
// Header files
#include "MudraEnvironment.h"
#include "MudraDevice.h"
```

## Initialization and Connection

### Android 

**Important:** Please insert your authentication token in your app *gradle.properties* file in order to use our sdk. See our github example (see <a href='https://github.com/wearable-devices/MudraAndroidReference'>here</a>).

Assuming the device is paired manually (for example - via the Android Bluetooth settings), there is no need for initial connection code.
Initialization is exposed via the auto connect function.

### Windows

Please call the Scan() function (note the indication in the console window) and press the button on you Mudra device. Once the a Mudra device is discovered, the OnDeviceDiscovered() callback will be called. 
It is possible to check the name of the new discovered device and filter it if it is not you device. Please note that you need to declare on which hand the device is worn, see SetHand().     
When you would like to terminate the connection, please call Close(). Please note that for c++ sdk usage, _WINDOWS define should be included in the c\c++ preprocessor properties.

### Logging
It is possible to set the logging severity by the following levels: Debug, Info, Warning and Error.
You can set the logging severity using the SetLoggingSeverity(). It is also possible to use your own function for logging using the SetOnLoggingMessageCallback() function.

```java
// Assuming the device is paired manually.
import MudraAndroidSDK.Mudra;
Mudra mudra = Mudra.autoConnectPaired(getContext());
```

```cpp
MudraEnvironment mudra;

void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{	
	if (device->GetName() != "MY_DEVICE_NAME")
                return;

 	// Insert all your device's callbacks here. 

	// You can connect your device on discovery like presented here, or save your device and connect it later. 
	device->SetHand(HandType::Left);
	mudra.Connect(device);
}

void OnLoggingMessageCallback(const string &msg)
{
	// Do something with msg
}

int main()
{
	// Logging
	mudra.SetLoggingSeverity(Logger::Severity::Debug);
	mudra.SetOnLoggingMessageCallback(OnLoggingMessageCallback);
	
	// Connection
	mudra.Scan(OnDeviceDiscovered);
	mudra.SetCalibration("<YOUR_PROJECT_PATH>\\Calibration.txt");
		
	// Do something here (set infinite loop here if you woould like to only listen to callbacks).

	// Close connection
	mudra.Close();
}
```

```csharp
using Mudra.SDK.Windows.DotNetFramework;

static void OnMudraDeviceDiscovered(MudraDevice device)
{	
	if (device.Name() != "MY_DEVICE_NAME")
                return;

 	// Insert all your device's callbacks here. 

	// You can connect your device on discovery like presented here, or save your device and connect it later. 
	device.SetHand(HandType.Left);
	MudraEnvironment.Connect(device);
}

static void OnLoggingMessageCallback(string &msg)
{
	// Do something with msg
}

static void main(string[] args)
{
	// Logging
	MudraEnvironment.SetLoggingSeverity(Logger::Severity::Debug);
	MudraEnvironment.SetOnLoggingMessageCallback(OnLoggingMessageCallback);
	
	// Connection
	MudraEnvironment.Scan(OnMudraDeviceDiscovered);
	MudraEnvironment.SetCalibration(HandType.Left, "<YOUR_PROJECT_PATH>\\Calibration.txt");
		
	// Do something here (set infinite loop here if you woould like to only listen to callbacks).

	// Close connection
	MudraEnvironment.Close();
}
```

```fsharp
﻿using UnityEngine;
using Mudra.Unity;

public class TestMudraUnityPlugin : MonoBehaviour
{
    // Instantiate Mudra plugin
    void Start()
    {
        try
        {
            Plugin.Instance.Init("<YOUR_PROJECT_PATH>\\Calibration.txt");
        }
        catch (System.Exception e)
        {
            print(e.ToString());
        }
    }

    // Clears information which was already accessed!
    private void LateUpdate()
    {
        Plugin.Instance.ClearFrame();
    }
    
    // Closes plugin after play\stop in Unity editor! 
    private void OnApplicationQuit()
    {
        Plugin.Instance.Close();
    }
}
```

## Fingertip Pressure

Functionality for estimating the amount of fingertip pressure. Returned values range between 0 and 1 and callbacks arrive at a rate of approximately 60fps. 

```java
Mudra.onFingertipPressureReady onFingertipPressureReady = new Mudra.onFingertipPressureReady() {
	@Override
	public void run(float v) {
		// Do something with v (this value is between 0 and 1) 
	}
};

mudra.setOnFingertipPressureReady(onFingertipPressureReady); // Enable 
mudra.setOnFingertipPressureReady(null); // Disable
```

```cpp
void OnProportionalReady(float proportional)
{
	cout << "\nProportionalfound = " << proportional;
}

void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{
	device->SetOnProportionalReady(OnProportionalReady);
}
```

```csharp
static void OnProportionalReady(float proportional)
{
	Console.Write("\nProportionalfound = {0}", proportional);
}

static void OnDeviceDiscovered(MudraDevice device)
{
	device.SetOnProportionalReady(OnProportionalReady);
}
```

```fsharp
private void UpdateFingerTipPressure()
{
	float? fingerTipPressure = Plugin.Instance.GetLastFingerTipPressure();

	if (fingerTipPressure != null)
	{
	    print($"FingerTipPressure {fingerTipPressure}");
	}
}

void Update()
{
	UpdateFingerTipPressure();
	//...
}
```


## Gesture Recognition

Functionality for predicting three gesture types: thumb, index and tap. Please check out the Mudra app for more info and example usage.

```java
Mudra.OnGestureReady onGestureReady = new Mudra.OnGestureReady() {
	@Override
	public void run(Mudra.GestureType gestureType) {
	    switch (gestureType) {
		case Tap:
		    // Do something here
		    break;
		case Index:
		    // Do something here
		    break;
		case Thumb:
		    // Do something here
		    break;
	    }
	}
};

mudra.setOnGestureReady(onGestureReady); // Enable 
mudra.setOnGestureReady(null); // Disable
```

```cpp
void OnGestureReady(GestureType gestureType)
{
	cout << "\nGesturefound = " << MudraEnvironment::ToString(gestureType);
}

void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{
	device->SetOnGestureReady(OnGestureReady);
}
```

```csharp
static void OnGestureReady(GestureType gesture)
{
	Console.Write("\OnGestureReady = {0}", gesture);
}

static void OnDeviceDiscovered(MudraDevice device)
{
	device.SetOnGestureReady(OnGestureReady);
}
```

```fsharp
private void UpdateGesture()
{
	GestureType? gesture = Plugin.Instance.GetLastGesture();
	if (gesture != null)
	{
	    print($"Gesture {gesture.ToString()}");
	}
}

void Update()
{
	UpdateGesture();
	//...
}
```

<aside class="notice"> Calibration is highly recommended for accurate gesture recognition. A calibration procedure is available in the Mudra app. Currently we make available an Android app, with more platform applications available soon. </aside>

## Air Mouse

Functionality for exposing an air mouse based on IMU quaternions. The Mudra air mouse projects raw quaternion values onto a 2d plane, which can be used to move a cursor.  

```java
int mScreenWidth, mScreenHeight; // Size of screen in pixels

Mudra.OnAirMousePositionChanged onAirMousePositionChanged = new Mudra.OnAirMousePositionChanged() {
        @Override
        public void run(float[] floats) {

            float airMousePosX += floats[0] * mScreenWidth * HSPEED;
            float airMousePosY += floats[1] * mScreenHeight * VSPEED;
            float airMousePosX = Clamp(mAirMousePosX, 0, mScreenWidth);
            float airMousePosY = Clamp(mAirMousePosY, 0, mScreenHeight);
        }
};

mudra.setOnAirMousePositionChanged(onAirMousePositionChanged); // Enable 
mudra.setOnAirMousePositionChanged(null); // Disable
```

```cpp
void OnAirMousePositionChanged(const vector<float>& position)
{
	cout << "\nX=" << position[0] << " ,Y=" << position[1];
}

void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{
	device->SetOnAirMousePositionChanged(OnAirMousePositionChanged);
}
```

```csharp
static void OnAirMousePositionChanged(float x, float y)
{
	Console.Write("\OnAirMousePositionChanged = {0}, {1}", x, y);
}

static void OnDeviceDiscovered(MudraDevice device)
{
	device.SetOnAirMousePositionChanged(OnAirMousePositionChanged);
}
```

```fsharp
private void UpdateAirMousePositionChange()
{
	float[] delta = Plugin.Instance.GetLastAirMousePositionChange();
	if (delta != null)
	{
	    print($"AirMouse position change {delta[0]}, {delta[1]}");
	}
}

void Update()
{
	UpdateAirMousePositionChange();
	//...
}
```

## Orientation

Functionality for exposing IMU based quaternion values. These values represent the orientation of your hand in a quaternion representation.

```java
Mudra.OnImuQuaternionReady onImuQuaternionReady = new Mudra.OnImuQuaternionReady() {
	@Override
	public void run(float[] data) {
		// Do something with data (quaternion, 4 values) 
	}
};

mudra.setOnImuQuaternionReady(onImuQuaternionReady); // Enable 
mudra.setOnImuQuaternionReady(null); // Disable
```

```cpp
void OnImuQuaternionPackageReady(vector<float>& data)
{
	cout << "\nReceived Imu Quaternion data";
}

void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{
	device->SetImuQuaternionPackageReady(OnImuAccNormPackageReady);
}
```

```csharp
static void OnImuQuaternionPackageReady(float[] data)
{
	for(int i=0; i<data.Length; i++)
	{ 
		Console.Write("\OnImuQuaternionPackageReady = {0}", data[i]);
	}
}

static void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{	
	device.SetImuQuaternionPackageReady(OnImuAccNormPackageReady);
}
```

```fsharp
private void UpdateImuQuaternion()
{
	float[] data = Plugin.Instance.GetLastImuQuaternionPackage();
	if (data == null)
	{
	    return;
	}
	string s1 = "";
	foreach (var d in data)
	{
	    s1 = s1 + d;
	}
	print($"Imu Quaternion {s1}");
}

void Update()
{
	UpdateImuQuaternion();
	//...
}
```

## Raw Data

Functionality for exposing raw SNC (Surface Nerve Conductance) sensor values. This function may incur an additional fee.  

```java
Mudra.OnSncReady onSncReady = new Mudra.OnSncReady() {
	@Override
	public void run(float[] floats) {
		// Packet data (3 sensors with 18 samples each - [0...17, 18...35, 36...53])
	}
};

mudra.setLicense(Feature.RawData, LICENSE); // private license 
mudra.setOnSncReady(onSncReady); // Enable 
mudra.setOnSncReady(null); // Disable
```

```cpp
void OnImuAccNormPackageReady(vector<float>& data)
{
	cout << "\nReceived Imu Norm data";
}

void OnSncPackageReady(const SncPackageData& data)
{
	cout << "\nReceived SNC data";
}

void OnDeviceDiscovered(std::shared_ptr<MudraDevice> device)
{
	device->SetImuAccNormPackageReady(OnImuQuaternionPackageReady);
	device->SetOnSncPackageReady(OnSncPackageReady);
}
```

```csharp
static void OnImuAccNormPackageReady(float[] data)
{
	for(int i=0; i<data.Length; i++)
	{ 
		Console.Write("\OnImuAccNormPackageReady = {0}", data[i]);
	}
}

static void OnSncPackageReady(int snc, float[] data)
{
	Console.Write("\OnSncPackageReady snc = {0}", snc);
	
	for(int i=0; i<data.Length; i++)
	{ 
		Console.Write("\OnSncPackageReady = {0}", data[i]);
	}
}

static void OnDeviceDiscovered(MudraDevice device)
{
	device.SetImuAccNormPackageReady(OnImuQuaternionPackageReady);
	device.SetOnSncPackageReady(OnSncPackageReady);
}
```

```fsharp
private void UpdateImuAccNorm()
{
	float[] data = Plugin.Instance.GetLastImuAccNormPackage();
	if (data == null)
	{
		return;
	}

	string s = "";
	foreach (var d in data)
	{
		s = s + d;
	}
	print($"Imu Acc Norm {s}");
}

private void UpdateSnc()
{
	for (int i = 0; i < 3; i++)
	{
	    float[] data = Plugin.Instance.GetLastSncPackage(i);
	    if (data == null)
	    {
		continue;
	    }

	    string s1 = "";
	    foreach (var d in data)
	    {
		s1 = s1 + d;
	    }
	    print($"Snc {i} data {s1}");
	}
}

void Update()
{
	UpdateImuAccNorm();
	UpdateSnc();
	//...
}
```

## Device Status

Functionality for exposing if the Mudra device is connected.

```java
Mudra.OnDeviceStatusChanged onDeviceStatusChanged = new Mudra.OnDeviceStatusChanged() {
        @Override
        public void run(boolean status) {
            if(status)
            {
                runOnUiThread(new Thread(new Runnable() {
                    public void run() {
                        TextView device_name= findViewById(R.id.txtDevicesNumber);
                        device_name.setText(mMudra.getBluetoothDevice().getAddress());
                    }
                }));
            }
        }
};

mudra.setOnDeviceStatusChanged(onDeviceStatusChanged); // Enable 
mudra.setOnDeviceStatusChanged(null); // Disable
```

## Battery Level

Functionality for exposing battery status.

```java
Mudra.OnBatteryLevelChanged onBatteryLevelChanged = new Mudra.OnBatteryLevelChanged() {
        @Override
        public void run(int i) {
            runOnUiThread(new Thread(new Runnable() {
                public void run() {
                    TextView txtBattery=findViewById(R.id.txtBatLevel);
                    txtBattery.setText(mMudra.getBatteryLevel() + "%");
                }
            }));
        }
};

Mudra.setOnBatteryLevelChanged(onBatteryLevelChanged); // Enable 
Mudra.setOnBatteryLevelChanged(null); // Disable 

```

