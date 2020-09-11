# Device Setup

For Castles Saturn S1F

## Installation steps

Installation on a device is performed in 4 separate installation functions:

 1. Prepare the device
 2. Install firmware / Castles Android Updates
 3. Install test security keys
 4. Install Kinetic Go Release

 ```
NOTE: If the device is restarted while in Debug mode, the security keys will be lost. Always exit Debug mode (if used) before restarting the device. If the security keys are lost, they can only be reinstalled by following the installation process starting with Factory Reset.
```

## 1. Prepare the device

 1. Factory reset the device
   * System panel -> Factory reset (System panel both passwords: 00000000)
 2. In System panel, make the following changes:
   1. Set Auto reboot to OFF
   2. Set Date and time:
     * Set NTP server to pool.ntp.org
     * Set timezone to your local timezone
   3. Connect to WiFi (Settings -> WLAN)
   4. set Debug mode to OFF

## 2. Install Firmware / Castles Android Updates

 1. Download the 
 [install package](https://kineticsmart-my.sharepoint.com/:u:/p/jim/ER6SD_gM185MqVvgr4Tlh1cBvJugHnnARvMWV29Sg3fb1g).
 This package contains the tools required to load software and security keys,
 as well as the software packages.
 2. Connect your computer to the Saturn device, making note of the COM port 
 assigned (Device Manager -> Ports (COM & LPT) -> "Qualcomm HS-USB..."
   * NOTE: Check the com port matches the assigned port each time before you use
   the loader
 3. Open the Loader.exe program on your computer (in the Tools folder of the 
 package), and on the Android device, find and start the "Loader" application
   1. Using the find files icon (folder with binoculars) locate the firmware 
   folder, and the **\Firmware -RAU16\1\02.SecureModule\signed** folder within, 
   select the file with the extension ".mci"
   2. Press the "Download" button in the Windows Loader application to start the
   load. File will load and complete
   3. On the Android device, press the restart button (refresh icon) and then
   the device is ready for the next update
   4. Use the find function in the windows Loader application to locate the
   **\Firmware - RAU16\2\01.Android\signed** folder and the ".mci" file and 
   press the "Download" button on the Windows Loader application

## 3. Install Security Keys

 1. Find and launch the "key\_injection\_tool" on your computer from the Tools 
 folder in the install package above
 2. Launch the "Key\_Injection" application on the Android device
 3. Go to "Initial Key(KEK)" tab in the key\_injection\_tool on your computer
   1. Key Storage: "TR31"
   2. KEK Key Type: 3DES
   3. Key Set: CFFF
   4. Key Index: 0000
 4. Uncheck "Use Component Key"
 5. Key Value: 00000000000000000000000000000000
   * NOTE: 32 'zero' characters
 6. Click "Inject", you can see this complete in the key\_injection\_tool window
 on your computer
 7. Go to "Working Key" tab
   1. Press "Add" button until you get 5 key slots in the table
   2. Fill with data from 
   [this table](https://kineticsmart-my.sharepoint.com/:x:/p/jim/EY_rInbUMtlBnab-S4YdBIUB81FZjyOXK8u6bvp35PDlHA?e=b8jcVr&wdLOR=c17B3895C-7B1D-4D0A-8D45-AC99C3A13C94) 
     * NOTE: make sure there are no spaces at the end of the keys before loading
   3. Click "Inject", you can see this complete in your key\_injection\_tool
   window

## 4. Install Kinetic Go Release

 1. Open the Loader.exe program on your computer
 2. Using the Find files icon (folder with binoculars) locate the **Nets Kinetic
 Go** application folder, and select the "all.mmci" file in the installation 
 package provided
 3. On the Android device, find and start the "Loader" application
 4. Press the "Download" button in the Windows Loader application to start the
 load. Files will load
 5. Once the file is loaded, ensure that debug mode is disabled, and restart the
 device
