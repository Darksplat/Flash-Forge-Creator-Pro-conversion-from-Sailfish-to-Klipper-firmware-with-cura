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

- [BillyNate](https://github.com/BillyNate)
  - Repository: [klipper-flashforge-creatorpro](https://github.com/BillyNate/klipper-flashforge-creatorpro)

Your contributions have been invaluable in the development and enhancement of tools related to FlashForge Creator Pro firmware and this guide.

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
     - **Arduino-ISP-Mod-UNO-4-Wifi Folder:**
       Containes the Arduino-ISP-Mod-UNO-4-Wifi sketch if using the UNO/Mega as an ISP

2. **Computer Setup:**
   - Install a text editor (e.g., Notepad++) for editing configuration files.
   - Download and install the necessary drivers for your 3D printer (https://www.flashforge.com/download-center/63)
   - Download and install the necessary drivers for your USBasp (https://zadig.akeo.ie)
   - Download and install the latest Arduino IDE (https://www.arduino.cc/en/software)
   - Download and install AVRDUDE (https://github.com/avrdudes/avrdude)
   - Download and install a ftp transfer program like Cyberduck(MacOS or Filezilla(Windows)
   - Download and install putty(win) or use your built in terminal program

3. **Klipper Firmware Files:**
   - Download the Klipper firmware files from the [official Klipper GitHub repository](https://github.com/KevinOConnor/klipper).

### Hardware Required

1. **Raspberry PI:**
   - Ensure you have a RPI4 or at the very least a RPI ZeroW 2 for octoprint/web interface/Webcam. (Not nesscary for actually converting firmware but nice to have for klipper)

2. **USB Cable:**
   - Ensure you have a USB cable to connect your printer to the computer for firmware flashing.

3. **Tools:**
   - **USBasp USBISP 3.3V/5V AVR Programmer:** You can use a USBasp programmer like [this one](https://core-electronics.com.au/usbasp-usbisp-3-3v-5v-avr-programmer.html).
   - **Arduino UNO or Mega:** If available, and you dont have a USBasp follow [this guide](https://docs.arduino.cc/built-in-examples/arduino-isp/ArduinoISP/) for ISP Programming an Arduino UNO. Use the sketch Arduino_ISP_Mod_UNO_4_Wifi.
   - **Set of Hex/Allen Keys:** Required to access the main board.
   - **Dupont Wires:** Have some on hand in case the cable from your USBasp/UNO/Mega is not long enough.

## Procedure

### Preparation

1. **Access the Motherboard:**
   - Lay the printer down or put it upside down and loosen the screws below it.
   - Carefully remove the metal cover to access the motherboard.
   - Locate the "8U2 ICSP" and "1280 ICSP" 6-pin header connectors.

   **ICSP Connectors:**
   
![ICSP Connectors](https://github.com/Darksplat/Flash-Forge-Creator-Pro-conversion-from-Sailfish-to-Klipper-firmware-with-cura/blob/main/ICSP%20Images/ffcp_motherboard_icsp.jpg)

  - Connect the USBasp cable at "8U2 ICSP" cheking the correct position. The picture below shows the reference pin.

![ICSP Connectors](https://github.com/Darksplat/Flash-Forge-Creator-Pro-conversion-from-Sailfish-to-Klipper-firmware-with-cura/blob/main/ICSP%20Images/ubsasp_icsp_position1.jpg)

![ICSP Connectors](https://github.com/Darksplat/Flash-Forge-Creator-Pro-conversion-from-Sailfish-to-Klipper-firmware-with-cura/blob/main/ICSP%20Images/ubsasp_icsp_position2.jpg)

   - Ensure the board is powered by USBasp or the power cable.
   - Open the command terminal and navigate to the AVRDUDE directory.

### Flash ATmega8U2 (USB to UART) Firmware

1. **Download the .hex Files:**
   - Download the .hex files from the [Bootloader repository](repository_link).
   - Copy the .hex files to your AVRDUDE folder:
     - Bootloader/8U2_firmware/Makerbot-usbserial.hex
     - Bootloader/MightyBoardFirmware-2560-bootloader/stk500boot_v2_mega2560.hex
         
MACOS

- Open Terminal and type 'cd' followed by a space then drag and drop the folder where avrdude and the hex files are (ie Downloads) in finder and press enter

**Using the USBasp as a Programmer:**

2. **Load 8U2 with Bootloader and Set Lock Bits:**
   ```bash
   avrdude -v -C avrdude.conf -p m8u2 -P usb -c usbasp -U flash:w:Makerbot-usbserial.hex:i -U lfuse:w:0xFF:m -U hfuse:w:0xD9:m -U efuse:w:0xF4:m -U lock:w:0x0F:m
   ```
   - If unsuccessful ("Error in USB Receive"), try again. It sometimes takes a few tries.

3. **Switch to "1280 ICSP":**
   - Take the USBasp cable off and connect it to "1280 ICSP" (attention with the cable position).

4. **Load ATMEGA2560 with Bootloader and Set Lock Bits:**
   ```bash
   avrdude -v -C avrdude.conf -p m2560 -c usbasp -P usb -U lock:w:0x3F:m -U efuse:w:0xFD:m -U hfuse:w:0xD8:m -U lfuse:w:0xFF:m -e
   avrdude -v -C avrdude.conf -p m2560 -P usb -c usbasp -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
   ```

5. **Using Arduino UNO/Mega as ISP Programmer:**
   - Follow the [Arduino ISP guide](https://docs.arduino.cc/built-in-examples/arduino-isp/ArduinoISP/) to set up your Arduino UNO or Mega as an ISP programmer, Use this sketch if required to around the old pins issue on a UNO 4 Wifi.

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
  
WINDOWS

### Flash ATmega2560 (Main Microcontroller) Bootloader (For Windows)

Flash ATmega8U2 (USB to UART) Firmware (Windows)

Load 8U2 with Bootloader and Set Lock Bits (Windows USBasp):
```bash
avrdude -v -C avrdude.conf -p m8u2 -P usb -c usbasp -U flash:w:Makerbot-usbserial.hex:i -U lfuse:w:0xFF:m -U hfuse:w:0xD9:m -U efuse:w:0xF4:m -U lock:w:0x0F:m
```
Switch to "1280 ICSP" (Windows USBasp):

Take the USBasp cable off and connect it to "1280 ICSP" (attention with the cable position).
Load ATMEGA2560 with Bootloader and Set Lock Bits (Windows USBasp):

```bash
avrdude -v -p m2560 -c usbasp -P usb -U lock:w:0x3F:m -U efuse:w:0xFD:m -U hfuse:w:0xD8:m -U lfuse:w:0xFF:m -e
avrdude -v -p m2560 -P usb -c usbasp -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
```
Load 8U2 with Bootloader and Set Lock Bits (Windows UNO/Mega):

 ```bash
avrdude -v -p m8u2 -c avrisp -P COMX -b 19200 -U flash:w:Makerbot-usbserial.hex:i -U lfuse:w:0xFF:m -U hfuse:w:0xD9:m -U efuse:w:0xF4:m -U lock:w:0x0F:m
```
Replace COMX with the appropriate COM port for your Arduino.

Switch to "1280 ICSP" (Windows UNO/Mega):
Take the USBasp cable off and connect it to "1280 ICSP" (attention with the cable position).
Load ATMEGA2560 with Bootloader and Set Lock Bits (Windows UNO/Mega):

```bash
avrdude -v -p m2560 -c avrisp -P COMX -b 19200 -U flash:w:stk500boot_v2_mega2560.hex:i -U lock:w:0x0f:m
```
Replace COMX with the appropriate COM port for your Arduino.

### About the dcnewman Distribution

We are using firmware for the 8U2 chip that was developed by the Arduino team.

This firmware translates virtual Serial DTR line toggles to a RESET output that connects to the reset pin on the Atmega1280. Serial implementations on Mac OSx and Linux pull the DTR line down on connect and release it on disconnect.

Mirroring the DTR to the 1280 reset means that the Bot will reset when it is connected to ReplicatorG. This is undesirable behavior if the Bot is in the middle of a print.

The new firmware ignores virtual DTR line changes. It pulls the RESET line down when it receives a new connection with a baud rate of 56700.

57600 is the baud rate we use for firmware updates. The RESET line is set high for all other baud rates. Normal communication with the bot uses 115200 baud.



## Klipper Installation

### Obtain a Klipper Configuration File

Most Klipper settings are determined by a "printer configuration file" stored on the Raspberry Pi. Follow these steps:

1. Look in the Klipper config directory for a file starting with a "printer-" prefix corresponding to the target printer.
2. If unavailable, search the printer manufacturer's website for an appropriate Klipper configuration file.
3. If none found, but the printer control board type is known, look for a config file starting with a "generic-" prefix.
4. Create a new custom printer configuration file if needed, using an appropriate example config file as a starting point.

### Prepping an OS Image

1. Install OctoPi on the Raspberry Pi computer, using OctoPi v0.17.0 or later.
2. Verify OctoPrint web server functionality.
3. SSH into the target machine and run the following commands:

    ```bash
    git clone https://github.com/Klipper3d/klipper
    ./klipper/scripts/install-octopi.sh
    ```

   This downloads Klipper, installs dependencies, sets up Klipper to run at startup, and starts the Klipper host software.

### Building and Flashing the Micro-Controller

1. Compile the micro-controller code:

Verify that is indeed an atmega2560

    ```bash
    cd ~/klipper/
    make distclean
    make menuconfig
    ```

Select the atmega2560, leave the rest as default

   Configure settings as per comments in the printer configuration file. Press "Q" to exit and then "Y" to save. Run:

    ```bash
    make
    ```

2. Determine the serial port connected to the micro-controller:

    ```bash
    ls /dev/serial/by-id/*
    ```

   Note the unique serial port name for flashing.

   This is my unique code but it should look like this just replace yours were mine is
   'usb-MakerBot_Industries_The_Replicator_85633323630351B050C0-if00'

4. Flash the micro-controller using avrdude:

    ```bash
    sudo service klipper stop
    avrdude -c stk500v2 -p m2560 -P /dev/serial/by-id/usb-MakerBot_Industries_The_Replicator_85633323630351B050C0-if00 -b 57600 -D -U out/klipper.elf.hex
    sudo service klipper start
    ```

   Update `/dev/serial/by-id/usb-MakerBot_Industries_The_Replicator_85633323630351B050C0-if00` with the actual unique serial port name.

### Configuring OctoPrint to Use Klipper

1. Configure OctoPrint:

    - Navigate to the Settings tab > Serial Connection.
    - Add `/tmp/printer` to "Additional serial ports" and save.
    - Change "Serial Port" setting to `/tmp/printer` and save.
    - In the Behavior sub-tab, select "Cancel any ongoing prints but stay connected to the printer" and save.

2. Connect OctoPrint to Klipper:

   - In the Connection section, set "Serial Port" to `/tmp/printer` and click "Connect."

3. In the Terminal tab, type "status" to verify communication between OctoPrint and Klipper.

### Configuring Klipper

1. Update the configuration file with the unique micro-controller name:

    ```bash
    ls /dev/serial/by-id/*
    ```

   Note the updated unique serial port name.

3. Restart OctoPrint and check the terminal for any configuration errors.

4. Once Klipper reports the printer is ready, proceed to the config check document for basic checks on the config file definitions.

Refer to the main documentation for additional information: [Klipper Installation Guide](https://www.klipper3d.org/Installation.html).

We now need to download the config file from klipper (https://github.com/Klipper3d/klipper/tree/master/config) These files do change all the time I will include mine for ease of use

Create the following directory in the config folder of klipper folder

klipper-flashforge-creatorpro

and copy these 2 files to it

creatorpro.cfg
creatorpro-macros.cfg

In the printer.cfg file add these lines to it and delete the rest

[include klipper-flashforge-creatorpro/creatorpro.cfg]

[include klipper-flashforge-creatorpro/creatorpro-macros.cfg]


It should look like this I have also included it in the klipper folder in the structure it needs to be in. You will notice that I have put my serial id in the file just as a reminder of what it is I sugguest you do the same the line is commented out any so it is just there for information.

[include mainsail.cfg]

[include klipper-flashforge-creatorpro/creatorpro.cfg]

[include klipper-flashforge-creatorpro/creatorpro-macros.cfg]


'# This file contains common pin mappings for the FlashForge-Creator-Pro
'# To use this config, the firmware should be compiled for
'# the Atmel atmega2560.

'# Use the following command to flash the board:
'#  avrdude -c stk500v2 -p m2560 -P /dev/serial/by-id/usb-MakerBot_Industries_The_Replicator_85633323630351B050C0-if00 -b 57600 -D -U out/klipper.elf.hex

'# See docs/Config_Reference.md for a description of parameters.

5. Update the config file:

    ```bash
    sudo service klipper stop
    nano ~/creatorpro.cfg
    sudo service klipper start
    ```

   Update the [mcu] section to use the new serial port name.
   
Now in the creatorpro.cfg file you need to insert the mcu serial id once again just copy your serial id to the file in place of mine save and close and restart





## Cura Installation for FlashForge Creator Pro

### Installation

#### Before You Begin

Please download the ZIP file and unzip it somewhere on your hard drive. Alternatively, you can use `git clone` to checkout the repository.

There are two ways to install the application: into the user folder (preferred) and the application package itself. Installing into the user folder will allow it to survive application updates, but if you have multiple user accounts on your Mac or PC, each user will need to install the profile separately, as it will not be visible to all users. In this case, you can use an alternative method to install the profile globally; however, it will not survive application updates as the application folder will be rewritten during the update.

**macOS**

1. **Into user library (preferred for single-user installations)**

   - This is a preferred way as it should survive application updates. The easiest way is to use the supplied script.
   - Open Terminal and navigate to the unzipped directory. Type `cd` and drag the unzipped folder into that Terminal window, then press Enter.
   - Launch the install script by executing `bash ./install.sh`.
   - Monitor output for any errors. If no errors are displayed, you are almost done - please refer to the post-install section below.

   **Alternatively, you can do it manually:**

   - Open Cura library folder located at `/Users/your_username/Library/Application\ Support/cura/5.5/` (for 5.5.x, for later versions it will be different).
   - Copy files from definitions, extruders, and meshes folders from the downloaded ZIP file (or cloned repository) into the respective folders in Cura. You may need to create the meshes folder first.
   - Perform steps from the post-install section below.

2. **Into Application (if you have multiple user accounts on your Mac that will need to use this printer)**

   - Open your Applications folder in Finder and right-click on Ultimaker Cura. Click on Show Package Contents.
   - Go to Contents/Resources/resources and copy files from definitions, extruders, and meshes folders from the downloaded ZIP file (or cloned repository) into the respective folders in Cura.
   - Perform steps from the post-install section below.

**Linux**

Change to the unzipped folder and run `bash ./install.sh`

**Windows**

1. **Into local AppData (preferred for single-user installations)**

   - This is a preferred way as it should survive application updates.
   - Open Cura library folder located at `C:\Users\your_username\AppData\Roaming\cura/5.5/` (for 5.5.x, for later versions it will be different).
   - Copy files from definitions, extruders, and meshes folders from the downloaded ZIP file (or cloned repository) into the respective folders in Cura. You may need to create the meshes folder first.
   - Perform steps from the post-install section below.

2. **Into the application folder (if you have multiple user accounts on your PC that will need to use this printer)**

   - Open Cura application resources folder located at `C:\Program Files\Ultimaker Cura 5.5\resources` (for 5.5.x, for later versions it will be different).
   - Copy files from definitions, extruders, and meshes folders from the downloaded ZIP file (or cloned repository) into the respective folders in Cura.
   - Perform steps from the post-install section below.

**Post-install**

1. Launch Cura and click on Add Printer in the printer selection dropdown. You should be able to see FlashForge Creator Pro in the list.
2. BEFORE SLICING, DISABLE UNUSED EXTRUDER if using just one nozzle!
3. Insert the following macros from klipper into the START G-Code section:
   START_PRINT
   PRIME_LINE
4. Insert the following macros into the END G-Code section:
   END_PRINT
5. Do a test print

   - Remember that extruder 1 is right, and extruder 2 is left. The easiest way to disable/enable a specific extruder is to go to the Settings menu and do it from there as they are named properly in it.
