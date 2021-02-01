# BLE Remote Control Sample Application

## Overview

The BLE Remote Control application is a single chip SoC compliant with HID over GATT Profile (HOGP). Supported features include key, microphone (voice over HOGP), Infrared Transmit (IR TX), and TouchPad.

During initialization the app registers with the LE stack, the WICED HID Device Library, and keyscan and external HW peripherals to receive various notifications including bonding complete, connection status change, peer GATT requests/commands, interrupts for key pressed/released, ADC audio, and Touchpad. Pressing any key will start LE advertising. When the device is successfully bonded, the app saves the bonded host's information in NVRAM. When the user presses/releases any key, a key report will be sent to the host. On connection up or battery level changed, a battery report will be sent to the host. When battery level is below shutdown voltage, device will critical shutdown. When the user presses and holds the microphone key, voice streaming starts until the user releases the microphone key. When the user presses and holds the power key, IR TX starts until the power key is released.

## Features demonstrated
- GATT database and device configuration initialization
- Registration with the LE stack for various events
- Sending HID reports to the host
- Processing write requests from the host
- Low power management
- Over the air firmware update (OTAFU)

# Instructions
To demonstrate the app, walk through the following steps:

1. Plug the WICED board or the 20819A1 Remote Control HW into your computer.
2. Place a jumper to bypass Serial Flash (i.e. jumper on J5 in CYW920819EVB\_02 board), then power up the board or Remote Control HW.
3. Remove the jumper so that download procedure below can write to Serial Flash.
4. Build and download the application (to the EVAL board or Remote Control HW).
5. If the download failed due to not able to detecting device, just repeat step 4 again.
6. Unplug the EVAL board or Remote Control HW from your computer (i.e. unplug the UART cable).
7. Power cycle the EVAL board or Remote Control HW.
8. Press any key to start LE advertising, then pair with a TV. If using the CYW920819EVB\_02 board, use a fly wire to connect GPIO P0 and P11 to simulate key '0' press, and remove the wire to simulate key release.
9. Once connected, it becomes the remote control of the TV.

If you have the 20819A1 Remote Control HW:

   - Press and hold the microphone key, voice streaming starts until the key is released.
   - Touch touchpad, touchpad report will be sent to the TV.

In case what you have is the WICED EVAL board, you can either use fly wire to connect to GPIOs to simulate key press and release. Or use the ClientControl tool in the tools to simulate key press and release.

1. Plug the WICED EVAL board into your computer. When using CYW920819EVB\_02/CYW920820EVB\_02, remove the 'PERIPHERAL ENABLE' J18 and 'THERMISTER ENABLE" J14 jumpers.
2. Build and download the application to the WICED board.
3. If failed to download due to device not detected, just repeat step 2 again.
4. Press any key to start LE advertising, then pair with a TV. Use a fly wire to connect GPIO P0 and P11 to simulate key '0' press, and remove the wire to simulate key release.
5. Once connected, it becomes the remote control of the TV.

To use ClientControl tool + WICED EVAL board to simulate key press and release, launch ClientControl from MTB Tools section of the QuickPanel. Make sure you use "TESTING\_USING\_HCI=1" in the application makefile. 208xx device and 20735 device firmware behaves differently. The following steps shows how to establish communication between ClientControl and the device.

For 20819/20820 devices:

1. Plug the hardware into your computer.
2. Build and download the application.
3. Run ClientControl.exe.
4. Choose 3M as the baud rate and select the serial port in ClientControl tool window.
5. Open the port and then reset the device. Close and re-open the port so the HID tab gets activated.

For 20735/20835 devices:

1. Plug the hardware into your computer.
2. Build and download the application.
3. Run ClientControl.exe.
4. Choose 3M as the baudrate and select the serial port in ClientControl tool window.
5. Reset the device. (Press reset button or unplug/plug the USB cable). Within 2 seconds, before the device enters deep sleep, open the port. If HIDD tab is not activated, close the port and repeat step 5. Once the HID tab is activated, the HID buttons will become available.
6. Click on "Enter Pairing Mode" to start advertisement, then pair with a PC or Tablet.
7. Once connected, it becomes the remote of the PC or Tablet.
8  Click on the key buttons, to send the key reports.  For example to send key down event when key '1' is pushed, report should be:<br/>
01 00 00 1e 00 00 00 00 00<br/>
When the key is released, it should send all keys up:<br/>
01 00 00 00 00 00 00 00 00.

## Notes

The application GATT database is located in wiced\_bt\_cfg.c. If you create a GATT database using Bluetooth Configurator, update the GATT database in the location mentioned above.

## Application Settings

- TESTING\_USING\_HCI
    - Use this option for testing with ClientControl. The ClientControl UI can be used to provide input. When this option is enabled, the device will not enter SDS/ePDS for power saving.

- OTA\_FW\_UPGRADE
    - Use this option for enabling firmware upgrade over the Air (OTA) capability. The peer tool applications in wiced\_btsdk\tools\btsdk-peer-apps-ota can be used to upgrade OTA firmware image. The OTA firmware image with extention *.ota.bin is created under the build folder. Due to limited internal FLASH space, the 20819/20820 device must use external FLASH for OTA firmware upgrade to be functional. OTAFU is not supported in CYW920819EVB-02 and CYW920820EVB-02 targets.

 - OTA\_SEC\_FW\_UPGRADE
    - Use this option for secure OTA firmware upgrade. OTA\_FW\_UPGRADE option must be enabled for this option to take effect. The OTA firmware image with extention *.ota.bin.signed must be used for OTA firmware upgrade. Please follow the instruction in wiced\_btsdk\tools\btsdk-peer-apps-ota\readme.txt to create *.ota.bin.signed file image.

- LE\_LOCAL\_PRIVACY
    - When enabled, the device uses RPA (Random Private Address). When disabled, the device uses Public static address.

- AUTO\_RECONNECT
    - Use this option to enable auto reconnect. By enabling this option, the device will always stay connected. If it is disconnected, it wil try to reconnect until it is connected. This option should be used together with  DISCONNECTED\_ENDLESS\_ADV. When this option is enabled, the HID device will always try to maintain a connection with the paired HID host; therefore, if the link is down, it will continuously try to reconnect. To conserve power, it should allow entering SDS/ePDS while advertising; thus, the DISCONNECTED\_ENDLESS\_ADV option should be enabled; otherwise, it may drain the battery quickly if the host was not available to reconnect.

DISCONNECTED\_ENDLESS\_ADV
    - Use this option to enable disconnected endless advertisement. When the link is disconnected, if AUTO\_RECONNECT is set, it will automatically start to advertise for reconnect. Otherwise, it will start to advertise after an external event, such as a key press event; i.e. the user will need to press a key to start reconnect. Once it starts to advertise, it will attempt to reconnect forever until it is connected. To conserve power, it allows SDS/ePDS while in low density advertisement with a long interval.

- SKIP\_PARAM\_UPDATE
    - Use this option to skip sending a link parameter update request. When this option is disabled, if the peer device (master) assigned link parameter is not within the device's preferred range, the device will send a request for the desired link parameter change. This option can be enabled to stop the device from sending the request and accept the given link parameter as is.

    - Background: In some OS (peer host), after the link is up, it continuously sends different parameters of LINK\_PARAM\_CHANGE over and over for some time. When the parameter is not in our device preferred range, the firmware was rejecting and renegotiating for new preferred parameters. It can lead up to endless and unnecessary overhead in link parameter changes. Instead of continuously rejecting the link parameter, by using this option, we accept peer requested link parameter as is and start a timer to send the final link parameter change request later when the peer host settles down in link parameter change.

- START\_ADV\_ON\_POWERUP
    - Use this option to start advertisements after power up (cold boot). By enabling this option, after power up, the device will automatically try to connect to the paired host if it is already paired. If it is not paired, it will enter discovery mode for pairing.

- ENABLE\_CONNECTED\_ADV
    - Use this option to allow advertisements for new host pairing while connected to a host.

- ENABLE\_EASY\_PAIR
    - Use this option to enable easy pairing. This is a proprietary method of pairing to bond connection between HID host and HID device. Instead sending advertising, the HID device will do scanning to find the preferred hosts in a list. Then select the strongest RSSI preferred host to bond the connection. To enable this function, the HID hosts must also need to enable EASY\_PAIRING method.

- ASSYMETRIC\_SLAVE\_LATENCY
    - Use this option to enable asymmetric slave latency.
    - Background: In early days, some HID host devices will always reject HID slave's link parameter update request. Because of this, the HID device will end up consuming high power when slave latency was short. To work around this issue, we use Asymmetric Slave Latency method to save power by waking up only at multiple time of the communication anchor point. When this option is enabled:

        * We do not send LL\_CONNECTION\_PARAM\_REQ.
        * We simply start Asymmetric Slave Latency by waking up at multiple times of given slave latency.

    - Since this is not a standard protocol, we do not recommend enabling this option unless if it is necessary to save power to work around some HID hosts.

- ENABLE\_FINDME
    - Use this option to enable Find Me profile.

- ENABLE\_AUDIO
    - Use this option to enable audio function to send voice over HOGP (HID over GATT Protocol). By default, mSBC encoding method is used unless one of the following option is enabled:

  - OPUS\_CELT\_ENCODER
     - Use Opus CELT encoder.

  - ADPCM\_ENCODER
     - Use ADPCM encoder.

  - If both OPUS\_CELT\_ENCODER and ADPCM\_ENCODER are defined, Opus CELT encoding method is used.

- ENABLE\_DIGITAL\_MIC
    - Use this option to enable/disable digital microphone (ENABLE\_DIGITAL\_MIC=1/0). ENABLE\_AUDIO option must be enabled for this option to take effect.

- ENABLE\_ANDROID\_AUDIO
    - When this option is enabled, the Android TV Voice Service is enabled. It forces the following options:

        * ENABLE\_AUDIO: Audio is enabled
        * ADPCM\_ENCODER: Forced to use ADPCM encoder (forced OPUS\_CELT\_ENCODER=0)
        * ENABLE\_DIGITAL\_MIC: The default is on and can be turned of by "ENABLE\_DIGITAL\_MIC=0"

- ENABLE\_TOUCHPAD
    - Use this option to enable touchpad functions. The option requires actual demo remote hardware to be functional.

- ENABLE\_IR
    - Use this option to enable IR functions. The option requires actual demo remote hardware to be functional. A sample dummy IR protocol is used. The developer will need to decide and implement the actual IR protocol.

## Key Matrix

When TESTING\_USING\_HCI is enabled, the key Matrix is disabled.

Key Matrix (ROW,COL)<br/>
Key index n = col*ROW + row<br/>
row = n % ROW<br/>
col = n / ROW<br/>

For example, to find the key index with row=1 and col=3 with key matrix 5x4, (ROW, COL)=(5, 4) key index n = col*ROW + row = 3*5 + 1 = 16

To find out row and col for index 16, row = 16 % 5 = 1, col = 16 / 3 = 3

20735:<br/>
Platform: CYW920735Q60EVB-01<br/>
Key Matrix (ROW,COL) = (5,4) = (P00..P04, P08..P11)

AUDIO Key Index     16 = (row=1, col=3) = (P01, P11) = (ARD\_RST, A3)<br/>
IR Key Index        17 = (row=2, col=3) = (P02, P11) = (D4, A3)<br/>
Pairing Key Index   18 = (row=3, col=3) = (P03, P11) = (D5, A3)<br/>

208xx:<br/>
Actual Remote platform (CYW920820REF-RM-01):<br/>
Key Matrix (ROW,COL) = (7,7) = (P00..P06, P08..P14)

AUDIO Key Index     23 = (row=2, col=3) = (P02, P11)<br/>
IR Key Index         0 = (row=0, col=0) = (P00, P08)<br/>
Pairing Key Index   29 = (row=1, col=4) = (P03, P11)<br/>

WICED EVAL Board platform (CYW9208xxEVB-02):<br/>
Since some pins are not available on wiced CYW9208xxEVB evaluation board, when TARGET=CYW9208xxEVB-02 is used the following 3 key indexes are translated to actual platform key index:

AUDIO Key Index     16 = (row=2, col=2) = (P02, P10) = (D4, A2) will be translate to key index 23<br/>
IR Key Index         0 = (row=0, col=0) = (P00, P08) = (D2, A0)<br/>
Pairing Key Index    8 = (row=1, col=1) = (P01, P09) = (ARD\_RST, D13) will be translated to key index 29

## Common application settings

Application settings below are common for all BTSDK applications and can be configured via the makefile of the application or passed in via the command line.

- BT\_DEVICE\_ADDRESS<br/>
    - Set the BDA (Bluetooth Device Address) for your device. The address is 6 bytes, for example, 20819A10FFEE. By default, the SDK will set a BDA for your device by combining the 7 hex digit device ID with the last 5 hex digits of the host PC MAC address.

- UART<br/>
    - Set to the UART port you want to use to download the application. For example 'COM6' on Windows or '/dev/ttyWICED\_HCI\_UART0' on Linux or '/dev/tty.usbserial-000154' on macOS. By default, the SDK will auto-detect the port.

- ENABLE_DEBUG<br/>
    - For HW debugging, select the option '1'. See the document [WICED-Hardware-Debugging](https://github.com/cypresssemiconductorco/btsdk-docs/blob/master/docs/BT-SDK/WICED-Hardware-Debugging.pdf) for more information. This setting configures GPIO for SWD.
      - CYW920819EVB-02/CYW920820EVB-02: SWD signals are shared with D4 and D5, see SW9 in schematics.
      - CYBT-213043-MESH/CYBT-213043-EVAL : SWD signals are routed to P02=SWDCK and P03=SWDIO. Use expansion connectors to connect VDD, GND, SWDCK, and SWDIO to your SWD Debugger probe.
	  - CYBT-223058-EVAL : SWD signals are routed to P02=SWDCK and P03=SWDIO. Use expansion connectors to connect VDD, GND, SWDCK, and SWDIO to your SWD Debugger probe.
	  - CYBT-243053-EVAL: SWD signals are routed to P12=SWDCK and P13=SWDIO. Use expansion connectors to connect VDD, GND, SWDCK, and SWDIO to your SWD Debugger probe.
	  - CYBT-253059-EVAL: SWD signals are routed to P12=SWDCK and P13=SWDIO. Use expansion connectors to connect VDD, GND, SWDCK, and SWDIO to your SWD Debugger probe.
      - CYW989820EVB-01: SWDCK (P02) is routed to the J13 DEBUG connector, but not SWDIO. Add a wire from J10 pin 3 (PUART CTS) to J13 pin 2 to connect GPIO P10 to SWDIO.
      - CYW920719B2Q40EVB-01: PUART RX/TX signals are shared with SWDCK and SWDIO. Remove RX and TX jumpers on J10 when using SWD. PUART and SWD cannot be used simultaneously on this board unless these pins are changed from the default configuration.
      - CYW920721B2EVK-02: SWD hardware debugging supported. SWD signals are shared with D4 and D5, see SW9 in schematics.
      - CYW920721B2EVK-03, CYW920721M2EVK-01: SWD hardware debugging is not supported.
      - CYW920721M2EVK-02: SWD hardware debugging is supported. The default setup uses P03 for SWDIO and P05 for SWDCK.
      - CYW920706WCDEVAL: SWD debugging requires fly-wire connections. The default setup uses P15 (J22 pin 3) for SWDIO and P30 (J19 pin 2) for SWDCK. P30 is shared with BTN1.
      - CYW920735Q60EVB-01: SWD hardware debugging supported. The default setup uses the J13 debug header, P3 (J13 pin 2) for SWDIO and P2 (J13 pin 4) for SWDCK.  They can be optionally routed to D4 and D4 on the Arduino header J4, see SW9 in schematics.
      - CYW920835REF-RCU-01: SWD hardware debugging is not supported.
      - CYW9M2BASE-43012BT: SWD hardware debugging is not supported.
      - CYW943012BTEVK-01: SWD hardware debugging is not supported.

## Building code examples

**Using the ModusToolbox IDE**

1. Install ModusToolbox 2.2.
2. In the ModusToolbox IDE, click the **New Application** link in the Quick Panel (or, use **File > New > ModusToolbox IDE Application**).
3. Pick your board for BTSDK under WICED Bluetooth BSPs.
4. Select the application in the IDE.
5. In the Quick Panel, select **Build** to build the application.
6. To program the board (download the application), select **Program** in the Launches section of the Quick Panel.


**Using command line**

1. Install ModusToolbox 2.2
2. On Windows, use Cygwin from \ModusToolbox\tools_2.2\modus-shell\Cygwin.bat to build apps.
3. Use the tool 'project-creator-cli' under \ModusToolbox\tools_2.2\project-creator\ to create your application.<br/>
   > project-creator-cli --board-id (BSP) --app-id (appid) -d (dir) <br/>
   See 'project-creator-cli --help' for useful options to list all available BSPs, and all available apps per BSP.<br/>
   For example:<br/>
   > project-creator-cli --app-id mtb-example-btsdk-empty --board-id CYW920706WCDEVAL -d .
4. To build the app call make build. For example:<br/>
   > cd mtb-examples-btsdk-empty<br/>
   > make build<br/>
6. To program (download to) the board, call:<br/>
   > make qprogram<br/>
7. To build and program (download to) the board, call:<br/>
   > make program<br/>

   Note: make program = make build + make qprogram


## Downloading an application to a board

If you have issues downloading to the board, follow the steps below:

- Press and hold the 'Recover' button on the board.
- Press and hold the 'Reset' button on the board.
- Release the 'Reset' button.
- After one second, release the 'Recover' button.

Note: this is only applicable to boards that download application images to FLASH storage. Boards that only support RAM download (DIRECT_LOAD) such as CYW9M2BASE-43012BT can be power cycled to boot from ROM.

## SDK software features

- Dual-mode Bluetooth stack included in the ROM (BR/EDR and LE)
- Bluetooth stack and profile level APIs for embedded Bluetooth application development
- WICED HCI protocol to simplify host/MCU application development
- APIs and drivers to access on-board peripherals
- Bluetooth protocols include GAP, GATT, SMP, RFCOMM, SDP, AVDT/AVCT, LE Mesh
- LE and BR/EDR profile APIs, libraries, and sample apps
- Support for Over-The-Air (OTA) upgrade
- Device Configurator for creating custom pin mapping
- Bluetooth Configurator for creating LE GATT Database
- Peer apps based on Android, iOS, Windows, etc. for testing and reference
- Utilities for protocol tracing, manufacturing testing, etc.
- Documentation for APIs, datasheets, profiles, and features
- BR/EDR profiles: A2DP, AVRCP, HFP, HSP, HID, SPP, MAP, PBAP, OPP
- LE profiles: Mesh profiles, HOGP, ANP, BAP, HRP, FMP, IAS, ESP, LE COC
- Apple support: Apple Media Service (AMS), Apple Notification Center Service (ANCS), iBeacon, Homekit, iAP2
- Google support: Google Fast Pair Service (GFPS), Eddystone
- Amazon support: Alexa Mobile Accessories (AMA)

Note: this is a list of all features and profiles supported in BTSDK, but some WICED devices may only support a subset of this list.

## List of boards available for use with BTSDK

- [CYW20819A1 chip](https://github.com/cypresssemiconductorco/20819A1)
    - [CYW920819EVB-02](https://github.com/cypresssemiconductorco/TARGET_CYW920819EVB-02), [CYBT-213043-MESH](https://github.com/cypresssemiconductorco/TARGET_CYBT-213043-MESH), [CYBT-213043-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-213043-EVAL), [CYW920819REF-KB-01](https://github.com/cypresssemiconductorco/TARGET_CYW920819REF-KB-01)
- [CYW20820A1 chip](https://github.com/cypresssemiconductorco/20820A1)
    - [CYW920820EVB-02](https://github.com/cypresssemiconductorco/TARGET_CYW920820EVB-02), [CYW989820EVB-01](https://github.com/cypresssemiconductorco/TARGET_CYW989820EVB-01), [CYBT-243053-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-243053-EVAL)
- [CYW20721B2 chip](https://github.com/cypresssemiconductorco/20721B2)
    - [CYW920721B2EVK-02](https://github.com/cypresssemiconductorco/TARGET_CYW920721B2EVK-02), [CYW920721B2EVK-03](https://github.com/cypresssemiconductorco/TARGET_CYW920721B2EVK-03), [CYW920721M2EVK-01](https://github.com/cypresssemiconductorco/TARGET_CYW920721M2EVK-01), [CYW920721M2EVK-02](https://github.com/cypresssemiconductorco/TARGET_CYW920721M2EVK-02), [CYBT-423060-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-423060-EVAL), [CYBT-483062-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-483062-EVAL), [CYBT-413061-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-413061-EVAL)
- [CYW20719B2 chip](https://github.com/cypresssemiconductorco/20719B2)
    - [CYW920719B2Q40EVB-01](https://github.com/cypresssemiconductorco/TARGET_CYW920719B2Q40EVB-01), [CYBT-423054-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-423054-EVAL), [CYBT-413055-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-413055-EVAL), [CYBT-483056-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-483056-EVAL)
- [CYW20706A2 chip](https://github.com/cypresssemiconductorco/20706A2)
    - [CYW920706WCDEVAL](https://github.com/cypresssemiconductorco/TARGET_CYW920706WCDEVAL), [CYBT-353027-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-353027-EVAL), [CYBT-343026-EVAL](https://github.com/cypresssemiconductorco/TARGET_CYBT-343026-EVAL)
- [CYW20735B1 chip](https://github.com/cypresssemiconductorco/20735B1)
    - [CYW920735Q60EVB-01](https://github.com/cypresssemiconductorco/TARGET_CYW920735Q60EVB-01)
- [CYW20835B1 chip](https://github.com/cypresssemiconductorco/20835B1)
    - [CYW920835REF-RCU-01](https://github.com/cypresssemiconductorco/TARGET_CYW920835REF-RCU-01)
- [CYW43012C0 chip](https://github.com/cypresssemiconductorco/43012C0)
    - [CYW9M2BASE-43012BT](https://github.com/cypresssemiconductorco/TARGET_CYW9M2BASE-43012BT), [CYW943012BTEVK-01](https://github.com/cypresssemiconductorco/TARGET_CYW943012BTEVK-01)

## Folder structure

All BTSDK code examples need the 'mtb\_shared\wiced\_btsdk' folder to build and test the apps. 'wiced\_btsdk' includes the 'dev-kit' and 'tools' folders. The contents of the 'wiced\_btsdk' folder will be automatically populated incrementally as needed by the application being used.

**dev-kit**

This folder contains the files that are needed to build the embedded Bluetooth apps.

* baselib: Files for chips supported by BTSDK. For example CYW20819, CYW20719, CYW20706, etc.

* bsp: Files for BSPs (platforms) supported by BTSDK. For example CYW920819EVB-02, CYW920721B2EVK-02, CYW920706WCDEVAL etc.

* btsdk-include: Common header files needed by all apps and libraries.

* btsdk-tools: Build tools needed by BTSDK.

* libraries: Profile libraries used by BTSDK apps such as audio, LE, HID, etc.

**tools**

This folder contains tools and utilities need to test the embedded Bluetooth apps.

* btsdk-host-apps-bt-ble: Host apps (Client Control) for LE and BR/EDR embedded apps, demonstrates the use of WICED HCI protocol to control embedded apps.

* btsdk-host-peer-apps-mesh: Host apps (Client Control) and Peer apps for embedded Mesh apps, demonstrates the use of WICED HCI protocol to control embedded apps, and configuration and provisioning from peer devices.

* btsdk-peer-apps-ble: Peer apps for embedded LE apps.

* btsdk-peer-apps-ota: Peer apps for embedded apps that support Over The Air Firmware Upgrade.

* btsdk-utils: Utilities used in BTSDK such as BTSpy, wmbt, and ecdsa256.

See README.md in the sub-folders for more information.

## ModusToolbox Tools

Source code generation tools installed by ModusToolbox installer:

- Device Configurator:
      A GUI tool to create custom pin mappings for your device.
- Bluetooth Configurator:
      A GUI tool to create and configure the LE GATT Database and BR/EDR SDP records for your application.

## Using BSPs (platforms)

BTSDK BSPs are located in the \mtb\_shared\wiced\_btsdk\dev-kit\bsp\ folder by default.

#### a. Selecting an alternative BSP

The application makefile has a default BSP. See "TARGET". The makefile also has a list of other BSPs supported by the application. See "SUPPORTED_TARGETS". To select an alternative BSP, use Library Manager from the Quick Panel to deselect the current BSP and select an alternate BSP. Then right-click the newly selected BSP and choose 'Set Active'.  This will automatically update TARGET in the application makefile.

#### b. Custom BSP

**Complete BSP**

To create and use a complete custom BSP that you want to use in applications, perform the following steps:

1. Select an existing BSP you wish to use as a template from the list of supported BSPs in the mtb\_shared\wiced\_btsdk\dev-kit\bsp\ folder.
2. Make a copy in the same folder and rename it. For example mtb\shared\wiced\_btsdk\dev-kit\bsp\TARGET\_mybsp.<br/>
   **Note:** This can be done in the system File Explorer and then refresh the workspace in Eclipse to see the new project.  Delete the .git sub-folder from the newly copied folder before refreshing in Eclipse.
   If done in the IDE, an error dialog may appear complaining about items in the .git folder being out of sync.  This can be resolved by deleting the .git sub-folder in the newly copied folder.

3. In the new mtb\_shared\wiced\_btsdk\dev-kit\bsp\TARGET\_mybsp folder, rename the existing/original (BSP).mk file to mybsp.mk.
4. In the application makefile, set TARGET=mybsp and add it to SUPPORTED\_TARGETS as well as TARGET\_DEVICE\_MAP.  For example: mybsp/20819A1
5. Update design.modus for your custom BSP if needed using the **Device Configurator** link under **Configurators** in the Quick Panel.
6. Update the application makefile as needed for other custom BSP specific attributes and build the application.

**Custom Pin Configuration Only - Multiple Apps**

To create a custom pin configuration to be used by multiple applications using an existing BSP that supports Device Configurator, perform the following steps:

1. Create a folder COMPONENT\_(CUSTOM)\_design\_modus in the existing BSP folder. For example mtb\_shared\wiced\_btsdk\dev-kit\bsp\TARGET\_CYW920819EVB-02\COMPONENT\_my\_design\_modus
2. Copy the file design.modus from the reference BSP COMPONENT\_bsp\_design\_modus folder under mtb\_shared\wiced\_btsdk\dev-kit\bsp\ and place the file in the newly created COMPONENT\_(CUSTOM)\_design\_modus folder.
3. In the application makefile, add the following two lines<br/>
   DISABLE\_COMPONENTS+=bsp\_design\_modus<br/>
   COMPONENTS+=(CUSTOM)\_design\_modus<br/>
   (for example COMPONENTS+=my\_design\_modus)
4. Update design.modus for your custom pin configuration if needed using the **Device Configurator** link under **Configurators** in the Quick Panel.
5. Building of the application will generate pin configuration source code under a GeneratedSource folder in the new COMPONENT\_(CUSTOM)\_design\_modus folder.

**Custom Pin Configuration Only - Per App**

To create a custom configuration to be used by a single application from an existing BSP that supports Device Configurator, perform the following steps:

1. Create a folder COMPONENT\_(BSP)\_design\_modus in your application. For example COMPONENT\_CYW920721B2EVK-03\_design\_modus
2. Copy the file design.modus from the reference BSP under mtb\_shared\wiced\_btsdk\dev-kit\bsp\ and place the file in this folder.
3. In the application makefile, add the following two lines<br/>
   DISABLE\_COMPONENTS+=bsp\_design\_modus<br/>
   COMPONENTS+=(BSP)\_design\_modus<br/>
   (for example COMPONENTS+=CYW920721B2EVK-03\_design\_modus)
4. Update design.modus for your custom pin configuration if needed using the **Device Configurator** link under **Configurators** in the Quick Panel.
5. Building of the application will generate pin configuration source code under the GeneratedSource folder in your application.


## Using libraries

The libraries needed by the app can be found in in the mtb\_shared\wiced\_btsdk\dev-kit\libraries folder. To add an additional library to your application, launch the Library Manager from the Quick Panel to add a library. Then update the makefile variable "COMPONENTS" of your application to include the library. For example:<br/>
   COMPONENTS += fw\_upgrade\_lib


## Documentation

BTSDK API documentation is available [online](https://cypresssemiconductorco.github.io/btsdk-docs/BT-SDK/index.html)

Note: For offline viewing, git clone the [documentation repo](https://github.com/cypresssemiconductorco/btsdk-docs)

BTSDK Technical Brief and Release Notes are available [online](https://community.cypress.com/community/software-forums/modustoolbox-bt-sdk)
