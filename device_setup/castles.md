# Castles Resources

The following resources are from Castles.

## Part 1: The Castles SDK installer and documentation

The files in this section are available on this
[link](https://drive.google.com/file/d/1CYJTQzG2CFwRsNgj5nZh72ASbfAZqmrX/view?usp=sharing)
and some of the files are also in the `device_resources` folder in this 
repository.

- Documentation
    - In the `SDK` folder:
        - `SATURN Series_Getting Start_Vx.x.pdf` A step by step guide on how
        to install the right Android Studio version and the Castles SDK 
        components.
        - `SATURNxxx_User Manual_vx.x.pdf` Specific information about the given
        version of the Saturn device, such as terminal components, basic
        operation, application loading and key injection.
        - `Castles Android API Reference Manual vx.x.pdf` Api reference for
        Castles SDK components for Castles Android terminals
    - In the `SDK/EMV` folder:
        - `EMV Contactless Library Configuration Reference Manual` a document 
        that illustrates the format of configuration file used by EMV 
        Contactless Library.
        - `EMV Contactless Setting Manual for VEGA3000` a document that 
        illustrates the minimum settings before using the EMV Contactless 
        Library.
        - `EMVL2 Application Configuration Reference Manual` a document that 
        illustrates the format of configuration file used by EMVL2 Application 
        Library.
- SDK Installation
    - In the `SDK` folder:
        - `CTAndroidSDK 1xxx` a directory that contains the installer for the 
        SDK components required for building applications for Castles Android 
        terminals. The `installset.ini` file from `device_resources` should be
        added to the folder containing the SDK installer before running the
        installer.
        - `CAS_CDC_Driver.zip` an archive containing a Castles CDC USB to UART
        driver for the Saturn terminal.
        - `SDK_Samples.rar` an archive that contains sample resources
    - In the `S1_ExtraSample` folder:
        - Aditional sample resources

## Part 2: The Terminal firmware and tools

The files in this section are available on this
[link](https://drive.google.com/file/d/1CaA8eTCfMr3V6U-4gl6kEeeX3GOBw-yE/view?usp=sharing).
The tools are used while developing and testing applications on the Castles
terminal device.

- Tools
    - `CAPGen application (version 2.08)` this tool will generate signed files 
    that can be loaded onto the Saturn terminal. It is recommended that the
    contents of the archive is extracted to the following path
    `C:\Program Files (x86)\Castles\SATURN1000\tools\` whereafter the application
    is found in 
    `C:\Program Files (x86)\Castles\SATURN1000\tools\S1-CAPGen (Evaluation) v2.08\`
    - `Loader utility (version 1.14)` this tool is for loading signed 
    application packages onto the Saturn terminal. The recommended extraction
    path is the same as above, after which the application is found in
    `C:\Program Files (x86)\Castles\SATURN1000\tools\Loader V1.14\`
    - `Key injection tool (version 1.13S)` this tool allows for the loading of
    keys onto the terminal. Again, same recommended extraction folder, after
    which the tool is available in
    `C:\Program Files (x86)\Castles\SATURN1000\tools\Key Injection Tool V1.13S\`
- Firmware update
    - In the `S1_FW` folder:
        - The latest version of the firmware and EMV library updates for the 
        terminalm as well as an updated JAR file to add to application projects
        - `CasHelp-HowtoupdatefirmwareinSaturnterminals-231018-1902` step by
        step instructions for how to load the latest firmware onto the Saturn
        terminal as well as add the latest JAR libraries to an application
        project.

## Debugging mode

There are resources in the Kinetic installation guide for enabling "Debug" mode
after installing the Kinetic firmware updates. However, here follows also the
Castles resources on this

- To enable showing the debug switch in settings, load the signed application
in `S1F-DebugBarOn.zip` archive (available in `device_resources` or on this
[link](https://drive.google.com/file/d/1D3OJwxw6noIpplRNd_gJf-_LJTaKre19/view?usp=sharing))
onto the device using the `Loader utility`
- To disable showing the debug switch in settings, load the signed application
in `S1F-DebugBarOff.zip` archive (available in `device_resources` or on this
[link](https://drive.google.com/file/d/1D7Zfvc9sMg0-_VILASET50lqoBfNL-h3/view?usp=sharing))
onto the device using the `Loader utility`
