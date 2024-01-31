# FlashForge Creator Pro: Converting from Sailfish to Klipper Firmware

## Acknowledgments

I would like to express gratitude to the following individuals, communities, and resources for their valuable contributions and support throughout this firmware transition process:

- The Klipper Development Team for creating and maintaining the Klipper firmware.
- The FlashForge Creator Pro community for sharing insights and experiences.
  
Special thanks to the following GitHub contributors and their repositories:

- [Damjan Ketukil](https://github.com/ketukil)
  - Repository: [FFCP_Marlin_Conversion](https://github.com/ketukil/FFCP_Marlin_Conversion)

- [Felipe Felipeksw](https://github.com/felipeksw)
  - Repository: [CreatorPro-Marlin-Cura](https://github.com/felipeksw/CreatorPro-Marlin-Cura)

- [Eugr](https://github.com/eugr)
  - Repository: [Flashforge-for-Cura](https://github.com/eugr/Flashforge-for-Cura)

Your contributions have been invaluable in the development and enhancement of tools related to FlashForge Creator Pro firmware.

⚠️ USE AT YOUR OWN RISK ⚠️

By using this guide you are fully aware of the risks involved by such modifications. I hereby take no responsibility for any loss and/or damage to property and/or personnel involved.

This setup was tested using:

Ultimaker Cura 5.5.0
Klipper
Flashforge Creator Pro (FF_CreatorBoard_REV D 20140320)
Compiled, flashed and sliced by Windows 10 and MacOS 13 (Ventura)


## Prerequisites

### Software Required

1. **Backup:** Before starting, back up configurations and settings from the current Sailfish firmware.
   - **FFCP Backup Firmware Folder:**
     - Firmware backups [UNTESTED]: I have not tried to restore said backups as I don't intend to go back to the Sailfish firmware.
     - **Atmega8U2 Folder:**
       Contains the firmware that acts like a USB to UART bridge and also performs the update of Sailfish firmware similarly to an ordinary Arduino.
     - **Atmega2560 Folder:**
       Contains the image of the main Sailfish firmware. All magic lives here and makes your printer do things... unimaginable things.

2. **Computer Setup:**
   - Install a text editor (e.g., Notepad++) for editing configuration files.
   - Download and install the necessary drivers for your 3D printer.
   - Download and install the necessary drivers for your USBasp
   - Download and install the latest Arduino IDE
   - Download and install AVRDUDESS
   - Download and install a ftp transfer program like Cyberduck(MacOS0 or Filezilla(Windows)
   - Download and install putty(win) or use your built in terminal program

3. **Klipper Firmware Files:**
   - Download the Klipper firmware files from the [official Klipper GitHub repository](https://github.com/KevinOConnor/klipper).

### Hardware Required

1. **USB Cable:**
   - Ensure you have a USB cable to connect your printer to the computer for firmware flashing.

2. **Tools:**
   - **USBasp USBISP 3.3V/5V AVR Programmer:** You can use a USBasp programmer like [this one](https://core-electronics.com.au/usbasp-usbisp-3-3v-5v-avr-programmer.html).
   - **Arduino UNO or Mega:** If available, and you dont have a USBasp follow [this guide](https://docs.arduino.cc/built-in-examples/arduino-isp/ArduinoISP/) for ISP Programming an Arduino UNO. Use the sketch ***Insert later***.
   - **Set of Hex/Allen Keys:** Required to access the main board.
   - **Dupont Wires:** Have some on hand in case the cable from your USBasp/UNO/Mega is not long enough.

