# futurerestore
_futurerestore is a hacked up idevicerestore wrapper, which allows manually specifying SEP and Baseband for restoring_

Only use if you are sure what you're doing.
---
Latest release available [here](https://github.com/s0uthwest/futurerestore/releases) for Windows, macOS & Linux.

I'll compile only macOS & Linux version, Windows version won't be compiled.

## Features
* Supports the following downgrade methods
  * Prometheus 64-bit devices (generator and APNonce collision mode);
  * Odysseus for 32-bit devices;
  * Re-restoring 32-bit devices to iOS 9 with [alitek123](https://github.com/alitek12) no-ApNonce method (alternative — [idevicererestore](https://github.com/s0uthwest/idevicererestore)).
* Allows restoring any non-matching signed iOS/SEP/Baseband.

__NOT recommended 'u' parameter, if you update from jailbroken firmware!__
# Dependencies
* ## Runtime
  * On macOS, futurerestore requires no runtime dependencies, the following are only for compiling;
  * On Linux, [usbmuxd](https://github.com/libimobiledevice/usbmuxd) is required at runtime;
* ## External Libs
  Required:
  * [libzip](https://github.com/nih-at/libzip);
  * [libfragmentzip](https://github.com/encounter/libfragmentzip);
  * [libplist](https://github.com/libimobiledevice/libplist);
  * [libirecovery](https://github.com/s0uthwest/libirecovery);
  * [libimobiledevice](https://github.com/libimobiledevice/libimobiledevice);
  
  Optional:
  * [libipatcher](https://github.com/s0uthwest/libipatcher);
* ## Submodules
  Make sure these projects compile on your system (install their dependencies)
  * [jssy](https://github.com/tihmstar/jssy);
  * [tsschecker](https://github.com/s0uthwest/tsschecker);
  * [img4tool](https://github.com/s0uthwest/img4tool);
  * [idevicerestore](https://github.com/s0uthwest/idevicerestore)
  
## Report an issue
You can do it [here](https://github.com/s0uthwest/futurerestore/issues).
  
## Compiling
Simple use  `bash autogen.sh && make` or use Xcode project.
  
### Some about curl for ubuntu
Follow [this guide](https://dev.to/jake/using-libcurl3-and-libcurl4-on-ubuntu-1804-bionic-184g) to use tsschecker on Ubuntu 18.04 (Bionic) as it requires libcurl3 which cannot co-exist with libcurl4 on this OS.

---

## 0) What futurerestore can do
**Downgrade/Upgrade/Re-restore same iOS.**
Whenever you read "downgrade" nowadays it means you can also upgrade and re-restore if you're on the same iOS. Basically this allows restoring an iOS and the installed iOS doesn't matter.

---

## 1) Prometheus (64-bit device) - generator method

### Requirements
- __Jailbreak;__
- Signing tickets (.shsh2) files with a generator;
- nonceEnabler patch enabled;

### Info
You can downgrade if the destination iOS is compatible with the latest signed SEP and if you have shsh2 files with a generator for that iOS.

### How to use
1. Device must be jailbroken and nonceEnabler patch must be active;
2. Open signing ticket (.shsh2) file and look up the generator:
  * Looks like this: `<key>generator</key><string>0xde3318d224cf14a1</string>`;
3. Write the boot-nonce generator to device's nvram
  * SSH into the device and run `nvram com.apple.System.boot-nonce=0xde3318d224cf14a1` to set the boot-nonce generator *0xde3318d224cf14a1*;
  * verify with `nvram -p` command

### Recommended methods to activate nonceEnabler patch
#### Method 1: ios-kern-utils (iOS 7.x-10.x)
1. Install DEB-file of [ios-kern-utils](https://github.com/Siguza/ios-kern-utils/releases/) on device;
2. Run on the device `nvpatch com.apple.System.boot-nonce`.

#### Method 2: Using special applications
Use utilities for setting boot-nonce generator — [PhœnixNonce](https://github.com/Siguza/PhoenixNonce) for iOS 9.x, [v0rtexnonce](https://github.com/arx8x/v0rtexnonce) for iOS 10.x, [nonceset1112](https://github.com/julioverne/NonceSet112) for iOS 11.0-11.1.2 and [noncereboot1131UI](https://github.com/s0uthwest/noncereboot1131UI) for iOS 11.0-11.4b3.

#### Method 3: [noncereboot11](https://github.com/pwn20wndstuff/noncereboot11) for iOS 11.x.
This CLI tool available at [pwn20wnd](https://github.com/pwn20wndstuff)'s [Cydia repo](http://xnu.science/repo). Install it and set boot-nonce generator with help on the binary.

### Activate tfp0 if jailbreak doesn't allow it
#### Method 1 (if jailbroken on 9.3.x)
  * reboot;
  * reactivate jailbreak with [Luca Todesco](https://github.com/kpwn)'s [JailbreakMe](https://jbme.qwertyoruiop.com/);
  * done.
  
#### Method 2 (if jailbroken on iOS 8.0-8.1 with [Pangu8](https://en.8.pangu.io))
  * install this [untether DEB-file](http://apt.saurik.com/beta/pangu8-tfp0/io.pangu.xuanyuansword8_0.5_iphoneos-arm.deb) with included tfp0 patch
  
#### Method 3 (if jailbroken on iOS 7.x with [Pangu7](https://en.7.pangu.io))
  * install this [untether DEB-file](http://apt.saurik.com/debs/io.pangu.axe7_0.3_iphoneos-arm.deb) with included tfp0 patch

#### Method 4
  * Use [cl0ver](https://github.com/Siguza/cl0ver) for iOS 9.x

---

## 2) Prometheus (64-bit device) - ApNonce collision method (Recovery mode);

### Requirements
- __iPhone 5s, iPad Air, iPad mini 2 (devices with A7 chip) on iOS 9.1 - 10.2;__
- Jailbreak doesn't required;
- Signing tickets files (.shsh, shsh2) with customly chosen ApNonce;
- The shsh file needs to have one of the ApNonces, which the device generates a lot;
- __collisioned ApNonces available in file 'nonces.txt' in [TSSChecker](https://github.com/s0uthwest/tsschecker) project.__

### Info
You can downgrade if the destination iOS is compatible with the latest signed SEP. You also need to have special signing tickets files. If you don't know what this is, you probably can **NOT** use this method!

### How to use
1. Connect your device in normal mode or recovery mode;
2. On the computer run `futurerestore -w -t ticket.shsh --latest-baseband --latest-sep ios.ipsw`.
* If you have saved multiple tickets with different ApNonces you can specify more than
one to speed up the process: `futurerestore -w -t t1.shsh -t t2.shsh -t t3.shsh -t t4.shsh --latest-baseband --latest-sep ios.ipsw`.

---

## 3) Prometheus (64-bit device) - ApNonce collision method (DFU)

### Requirements
- __Devices for A7 chip (iPhone 5s, iPad Air, iPad mini 2) and some devices with A8 chip (iPod touch [6th gen]) on all iOS firmwares;__
- Jailbreak doesn't required;
- Signing tickets files (.shsh, .shsh2) with customly chosen ApNonce;
- Ticket file needs to have one of the ApNonces, which the device generates a lot;
- __[img4tool](https://github.com/s0uthwest/img4tool) can't be used for Windows [problem with signing iBSS/iBEC], now it's TO-DO;__
- __collisioned ApNonces available in file 'nonces.txt' in [TSSChecker](https://github.com/s0uthwest/TSSChecker) project.__

### Info
You can downgrade if the destination iOS is compatible with the latest signed SEP. You also need to have special signing tickets files. If you don't know what this is, you probably can **NOT** use this method!

### How to use
1. Connect your device in DFU mode;
2. Use [irecovery](https://github.com/libimobiledevice/libirecovery) for check nonce booted with DFU;
3. Extract iBSS/iBEC from target firmware for downgrade (unsigned);
4. Check DFU ApNonces with [irecovery](https://github.com/libimobiledevice/irecovery) with DFU booting.
    You can't automatically collision DFU ApNonces.
    
    __If ApNonce is not collisioned, "use hands" for DFU booting.__
    
    __If ApNonce is successfully coliisioned, use this SHSH2 for sign iBSS/iBEC.__
5. Use [img4tool](https://github.com/s0uthwest/img4tool/releases) for sign iBSS:
   `img4tool -s ticket.shsh -c iBSS.signed -p <original_iBSS>`;
6. Use [img4tool](https://github.com/s0uthwest/img4tool/releases) for sign iBEC:
   `img4tool -s ticket.shsh -c iBEC.signed -p <original_iBEC>`;
7. So, after signing we can boot into Recovery with [irecovery](https://github.com/libimobiledevice/irecovery):

   `irecovery -f iBSS.signed` - loading iBSS;
   
   `irecovery -f iBEC.signed` - loading iBEC;
8. So good! On the computer run `futurerestore -w -t ticket.shsh --latest-baseband --latest-sep ios.ipsw`.

---

## 4) Odysseus (32-bit devices)

### Requirements
- futurerestore compiled with [libipatcher](https://github.com/s0uthwest/libipatcher) ([Odysseus](https://dayt0n.com/articles/Odysseus) support);
- Jailbreak or bootrom exploit (limera1n);
- Firmware keys for the device/destination iOS must be public;
- Signing tickets files (.shsh, .shsh2) for the destination iOS (OTA blobs work too!);
- _Odysseus bundle (You can use any successfully created bundle for this)._

### Info
If you have a jailbroken 32-bit device you can downgrade to any iOS you have signing tickets for. You can still get OTA tickets for some devices and use those, if there are signed.

### How to use
1. Get device into kDFU/pwnDFU mode:
  * Pre-iPhone 4s (limera1n devices):
    * Enter pwndfu mode with redsn0w or any other tool;
  * iPhone 4s and later:
    * Jailbreak required;
    * Enter kDFU mode by loading a pwnediBSS from any existing odysseus bundle.
2. Connect your device to computer in kDFU mode (or pwnDFU mode)
3. On the computer run `futurerestore --use-pwndfu -t ticket.shsh --latest-baseband ios.ipsw`

---

## 5) iOS 9 Re-restore bug (found by [@alitek123](https://github.com/alitek12), 32-bit devices only):
### Requirements
- Jailbreak doesn't required;
- __Signing tickets files (.shsh, .shsh2) without a ApNonce (noNonce APTickets);__

### Info
If you have signing tickets files for iOS 9.x which do not contain an ApNonce, you can restore to that firmware.

### How to use
1. Connect your device in DFU mode;
2. On the computer run `futurerestore -t ticket.shsh --latest-baseband ios9.ipsw`

## Credits
Creator of [original project](https://github.com/tihmstar/futurerestore) - [tihmstar](https://github.com/tihmstar).


ReadMe updated on:
        2019-01-21
