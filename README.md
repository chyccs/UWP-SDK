Neo smartpen SDK for Windows Universal Platform(UWP)
===================

## Universal Windows Platform(UWP) SDK 2.0 

UWP SDK for Windows 10. This library allows you to integrate the Neo smartpen into your app.

SDK was made to use Neo smartpen for Windows 10 (UWP). SDK library is built and published to [Nuget](https://www.nuget.org/packages/Neosmartpen.Net/)

## About Neo smartpen 

The Neo smartpen is designed to seamlessly integrate the real and digital worlds by transforming what you write on paper - everything from sketches and designs to business meeting notes - to your iOS, Android and Windows devices. It works in tandem with N notebooks, powered by NeoLAB Convergence’s patented Ncode™ technology and the accompanying application, Neo Notes. Find out more at www.neosmartpen.com
 - Tutorial video - https://goo.gl/MQaVwY
 
## About Ncode™: service development guide 

‘Natural Handwriting’ technology based on Ncode™(Microscopic data patterns containing various types of data) is a handwriting stroke recovery technology that digitizes paper coordinates obtained by optical pen devices such as Neo smartpen. The coordinates then can be used to store handwriting stroke information, analyzed to extract meaning based on user preferences and serve as the basis for many other types of services. 

Click the link below to view a beginners guide to Ncode technology. 
[https://github.com/NeoSmartpen/Documentations/blob/master/Ncode™ Service Development Getting Started Guide v1.01.pdf](https://github.com/NeoSmartpen/Documentations/blob/master/Ncode%E2%84%A2%20Service%20Development%20Getting%20Started%20Guide%20v1.01.pdf)


## Requirements

 - Visual Studio 2017
 - Install the Universal Windows Platform tools provided by Microsoft
 - Min version [10.0; build 15063]
 - Need to MarkerMrtro.Unity.Ionic.Zlib library (Nuget Package)
 - Standard Bluetooth Dongles ( Bluetooth Specification Version 4.0 or later with Microsoft Bluetooth stack )

## Dependencies

 - MarkerMrtro.Unity.Ionic.Zlib

## Supported models

- All models of Neo smartpen


## Getting Started with Sample App

At first, download the zip file containing the current version. You can unzip the archive and open NeosmartpenSDK_UWP.sln in Visual Studio 2017.

[download](https://github.com/NeoSmartpen/UWP-SDK/archive/master.zip)


## Using SDK library

Just install Neosmartpen.Net package in the Nuget

    Install-Package Neosmartpen.Net
    
Let's getting started using the API

## Api References

SDK API references page : [References](https://neosmartpen.github.io/UWP-SDK/docs)

## Sample Code

#### Notice

SDK handle data and commucation with peer device in other thread. So if you want data get from pen to appear in UI,  than you have to execute in UI thread.

#### Create GenericBluetoothPenClient and PenController instance
```cs
// create PenController instance.
// PenController control all pen event method
var controller = new PenController();

// Create GenericBluetoothPenClient instance. and bind PenController.
// GenericBluetoothPenClient is implementation of bluetooth function.
var client = new GenericBluetoothPenClient(controller);
```
#### Find Bluetooth Devices

You can find bluetooth device using below methods. And get **PenInformation** object that has bluetooth device information.

###### Find device
```cs
List<PenInformation> penList = await client.FindDevices();
```
###### Using LE Advertisement Watcher

```cs
// bluetooth watcher event
client.onStopSearch += onStopSearch;
client.onUpdatePenController += onUpdatePenController;
client.onAddPenController += onAddPenController;

// start watcher
client.StartLEAdvertisementWatcher();

// Event that is called when a device is added by the watcher
private void onAddPenController(IPenClient sender, PenInformation args)
{
}

// Event that is called when a device is updated
private void onUpdatePenController(IPenClient sender, PenUpdateInformation args)
{
}

// Event that is called when the watcher operation has been stopped
private void onStopSearch(IPenClient sender, Windows.Devices.Bluetooth.BluetoothError args)
{
}
```
#### Connect with device

```cs
// penInfomation is PenInformation class object what can be obtained from find device method
bool result = await client.Connect(penInfomation);
```

#### After Connection is established.

```cs
// add event in init method
controller.Connected += connected;
controller.PasswordRequested += passwordRequested;
controller.Authenticated += authenticated;

// It is called when connection is established ( You cannot use function on your device without authentication )
private void connected(IPenClient sender, ConnectedEventArgs args)
{
	System.Diagnostics.Debug.WriteLine(String.Format("Mac : {0}\r\n\r\nName : {1}\r\n\r\nSubName : {2}\r\n\r\nFirmware Version : {3}\r\n\r\nProtocol Version : {4}", args.MacAddress, args.DeviceName, args.SubName, args.FirmwareVersion, args.ProtocolVersion));
}

// If your device is locked, it is called to input password.
private void passwordRequested(IPenClient sender, PasswordRequestedEventArgs args)
{
	System.Diagnostics.Debug.WriteLine($"Retry Count : {args.RetryCount}, ResetCount :  {args.ResetCount }");
    _controller.InputPassword(password);
}

// If your pen is not locked, or authentication is passed, it will be called.
// When it is called, You can use all function on your device.
private void authenticated(IPenClient sender, object args)
{
}
```

#### Handling a handwriting data from peer device
```cs
// add event in init method
controller.DotReceived += dotReceived;

// Identifier of note(paper) (it is consist of section and owner, note)
int section = 1;
int owner = 1;
int note = 102;

// Requests to set your note type.
controller.AddAvailableNote(section, owner, note);

// event that is called when writing data is received
private void dotReceived(IPenClient sender, DotReceivedEventArgs args)
{
// TODO : You should implements code using coordinate data.
}
```

#### Querying list of offline data in Smartpen's storage

```cs
// add event in init method
controller.OfflineDataListReceived += offlineDataListReceived;

// Request offline data list
controller.RequestOfflineDataList();

// Event method to receive offline data list
private void offlineDataListReceived(IPenClient sender, OfflineDataListReceivedEventArgs args)
{
}

```

#### Downloading offline data in Smartpen's storage
```cs
// add event in init method
controller.OfflineDataDownloadStarted += offlineDataDownloadStarted;
controller.OfflineStrokeReceived += offlineStrokeReceived;
controller.OfflineDownloadFinished += offlineDownloadFinished;

// it is invoked when begins downloading offline data
private void offlineDataDownloadStarted(IPenClient sender, object args)
{
}

// it is invoked when it obtained offline data ( it can be called several times )
private void offlineStrokeReceived(IPenClient sender, OfflineStrokeReceivedEventArgs args)
{
}

// it is invoked when finished downloading
private void offlineDownloadFinished(IPenClient sender, SimpleResultEventArgs args)
{
}
```

## LICENSE

Copyright©2017 by NeoLAB Convergence, Inc. All page content is property of NeoLAB Convergence Inc. <https://neolab.net> 

### GPL License v3 - for non-commercial purpose
    
For non-commercial use, follow the terms of the GNU General Public License. 

GPL License v3 is a free software: you can run, copy, redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation. 

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. 

You should have received a copy of the GNU General Public License along with the program. If not, see <https://www.gnu.org/licenses/>. 


### Commercial license - for commercial purpose 

For commercial use, it is not necessary or required to open up your source code. Technical support from NeoLAB Convergence, inc is available upon your request. 

Please contact our support team via email for the terms and conditions of this license. 

- Global: _global1@neolab.net
- Korea: _biz1@neolab.net



## Opensource Library

Please refer to the details of the open source library used below.

### 1. MarkerMetro.Unity.Ionic.Zlib (https://github.com/MarkerMetro/MarkerMetro.Unity.Ionic.Zlib/blob/master/LICENSE)

The MIT License (MIT)

Copyright (c) 2015 Marker Metro

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---
**Feel free to leave any comment or feedback [here](https://github.com/NeoSmartpen/UWP-SDK/issues)**
