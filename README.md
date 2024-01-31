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
     - Firmware backups [UNTESTED]: I have not tried to restore said backups
     - **Atmega8U2 Folder:**
       Contains the firmware that acts like a USB to UART bridge and also performs the update of Sailfish firmware similarly to an ordinary Arduino.
     - **Atmega2560 Folder:**
       Contains the image of the main Sailfish firmware.

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
   - **Arduino UNO or Mega:** If available, and you dont have a USBasp follow [this guide](https://docs.arduino.cc/built-in-examples/arduino-isp/ArduinoISP/) for ISP Programming an Arduino UNO. Use the sketch Arduino_ISP_Mod_UNO_4_Wifi.
   - **Set of Hex/Allen Keys:** Required to access the main board.
   - **Dupont Wires:** Have some on hand in case the cable from your USBasp/UNO/Mega is not long enough.
  

Certainly! I'll integrate the code for using an Arduino UNO or Mega as the ISP programmer into the existing procedure:

Certainly! I'll integrate the provided commands into the existing procedure:

## Procedure

### Preparation

1. **Access the Motherboard:**
   - Lay the printer down or put it upside down and loosen the screws below it.
   - Carefully remove the metal cover to access the motherboard.
   - Locate the "8U2 ICSP" and "1280 ICSP" 6-pin header connectors.

   **ICSP Connectors:**
   - Connect the USBasp cable to "8U2 ICSP," ensuring correct positioning.
   ![ICSP Connectors](link_to_image)

   - Ensure the board is powered by USBasp or the power cable.
   - Open the command terminal and navigate to the AVRDUDE directory.

### Flash ATmega8U2 (USB to UART) Firmware

1. **Download the .hex Files:**
   - Download the .hex files from the [Bootloader repository](repository_link).
   - Copy the .hex files to your AVRDUDE folder:
     - Bootloader/8U2_firmware/Makerbot-usbserial.hex
     - Bootloader/MightyBoardFirmware-2560-bootloader/stk500boot_v2_mega2560.hex

2. **Load 8U2 with Bootloader and Set Lock Bits (using USBasp on macOS):**
   ```bash
   avrdude -v -C avrdude.conf -p m8u2 -P usb -c usbasp -U flash:w:Makerbot-usbserial.hex:i -U lfuse:w:0xFF:m -U hfuse:w:0xD9:m -U efuse:w:0xF4:m -U lock:w:0x0F:m
   ```
   - If unsuccessful ("Error in USB Receive"), try again. It sometimes takes a few tries.

3. **Switch to "1280 ICSP":**
   - Take the USBasp cable off and connect it to "1280 ICSP" (attention with the cable position).

4. **Load ATMEGA2560 with Bootloader and Set Lock Bits (using USBasp on macOS):**
   ```bash
   avrdude -v -C avrdude.conf -p m2560 -c usbasp -P usb -U lock:w:0x3F:m -U efuse:w:0xFD:m -U hfuse:w:0xD8:m -U lfuse:w:0xFF:m -e
   avrdude -v -C avrdude.conf -p m2560 -P usb -c usbasp -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
   ```

5. **Using Arduino UNO/Mega as ISP Programmer (on macOS):**
   - Follow the [Arduino ISP guide](https://docs.arduino.cc/built-in-examples/arduino-isp/ArduinoISP/) to set up your Arduino UNO or Mega as an ISP programmer.

6. **Load 8U2 with Bootloader and Set Lock Bits (using Arduino UNO/Mega on macOS):**
   ```bash
   avrdude -v -C avrdude.conf -p m8u2 -c avrisp -P /dev/ttyACM0 -b 19200 -U flash:w:Makerbot-usbserial.hex:i -U lfuse:w:0xFF:m -U hfuse:w:0xD9:m -U efuse:w:0xF4:m -U lock:w:0x0F:m
   ```
   - Replace `/dev/ttyACM0` with the appropriate port for your Arduino.

7. **Switch to "1280 ICSP" (using Arduino UNO/Mega on macOS):**
   - Take the USBasp cable off and connect it to "1280 ICSP" (attention with the cable position).

8. **Load ATMEGA2560 with Bootloader and Set Lock Bits (using Arduino UNO/Mega on macOS):**
   ```bash
   avrdude -v -C avrdude.conf -p m2560 -c avrisp -P /dev/ttyACM0 -b 19200 -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
   ```
   - Replace `/dev/ttyACM0` with the appropriate port for your Arduino.

### Flash ATmega2560 (Main Microcontroller) Bootloader (Continued for Windows)

9. **Load ATMEGA2560 with Bootloader and Set Lock Bits (using USBasp on Windows):**
   ```bash
   avrdude -v -C avrdude.conf -p m2560 -c usbasp -P usb -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
   ```

10. **Using Arduino UNO/Mega as ISP Programmer (on Windows):**
   - Follow the [Arduino ISP guide](https://docs.arduino.cc/built-in-examples/arduino-isp/ArduinoISP/) to set up your Arduino UNO or Mega as an ISP programmer.

11. **Load 8U2 with Bootloader and Set Lock Bits (using Arduino UN

O/Mega on Windows):**
   ```bash
   avrdude -v -C avrdude.conf -p m8u2 -c avrisp -P COMX -b 19200 -U flash:w:Makerbot-usbserial.hex:i -U lfuse:w:0xFF:m -U hfuse:w:0xD9:m -U efuse:w:0xF4:m -U lock:w:0x0F:m
   ```
   - Replace `COMX` with the appropriate COM port for your Arduino.

12. **Switch to "1280 ICSP" (using Arduino UNO/Mega on Windows):**
   - Take the USBasp cable off and connect it to "1280 ICSP" (attention with the cable position).

13. **Load ATMEGA2560 with Bootloader and Set Lock Bits (using Arduino UNO/Mega on Windows):**
   ```bash
   avrdude -v -C avrdude.conf -p m2560 -c avrisp -P COMX -b 19200 -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
   ```
   - Replace `COMX` with the appropriate COM port for your Arduino.

### About the dcnewman Distribution

We are using firmware for the 8U2 chip that was developed by the Arduino team.

This firmware translates virtual Serial DTR line toggles to a RESET output that connects to the reset pin on the Atmega1280. Serial implementations on Mac OSx and Linux pull the DTR line down on connect and release it on disconnect.

Mirroring the DTR to the 1280 reset means that the Bot will reset when it is connected to ReplicatorG. This is undesirable behavior if the Bot is in the middle of a print.

The new firmware ignores virtual DTR line changes. It pulls the RESET line down when it receives a new connection with a baud rate of 56700.

57600 is the baud rate we use for firmware updates. The RESET line is set high for all other baud rates. Normal communication with the bot uses 115200 baud.
