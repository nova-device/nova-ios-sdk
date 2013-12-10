Nova iOS SDK
============

Official iOS SDK for Nova.

Coming soon. Watch this repository for updates.

https://wantnova.com/

https://wantnova.com/sdk/


Usage
-----

### Initialization

Initialize the Nova flash service. A good time to do this is in `applicationDidFinishLaunching:`.

```objective-c
#import "NVFlashService.h"

// Setup. Do this when app starts.
// A good time is applicationDidFinishLaunching:
NVFlashService *flashService = [[NVFlashService alloc] init];

// Set which Nova devices to automatically pair with.
// Options: NVAutoPairClosest, NVAutoPairAll or NVAutoPairNone.
// This can be changed at any time while the app is running
// and it will reconnect.
flashService.autoPairMode = NVAutoPairClosest;
```

In `applicationDidBecomeActive:` you should enable the service, which
activates the Bluetooth radio and begins scanning:

```objective-c
[flashService enable];
```

In `applicationWillResignActive:` you should disable the service, which
shuts down the BluetoothLE scanning, conserving the battery life of
both the iPhone and Nova:

```objective-c
[flashService disable];
```


### Monitoring connection status

TODO


### Choosing the flash temperature/brightness settings.

```objective-c
NVFlashSettings *flashSettings = NVFlashSettingsWarm();

                           // or NVFlashSettingsGentle()
                           // or NVFlashSettingsFull()
                           // or NVFlashSettingsCustom(brightness, colorTemp)
```

### Trigger the flash

Because there can be slight (and hard to predict) latencies in Bluetooth
and also in the iPhone camera shutter time, the sequence is coordinated
by a sequence of callbacks.

```objective-c

// Step 1: Tell flash to activate.
[flashService beginFlash:flashSettings withCallback:^ (BOOL success) {
  // Step 2: This callback is called when the flash has acknowledged that
  //         it is lit.

  // Check whether flash activated succesfully.
  if (success) {

    // Step 3: Tell camera to take photo.
    [myCameraAbstraction takePhotoWithCallback:(^ {

      // Step 4: When photo has been captured, turn the flash off.
      [flashService:endFlash];
    })];

  } else {
    // Error: flash could not be triggered.
  }
}];
```

