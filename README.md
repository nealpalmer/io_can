# IO_CAN
CAN GPIO device (4 GPIO pins, input, output, PWM, NeoPixel, 4x ADC, 2x DAC, 5V out, 6-25V in, CAN 1Mb/s)

This is a circuit boards to plug into the FRC can bus, and give you 4 GPIO pins for use in software.  This should help reduce the complexity of wiring, by putting the GPIO near the device.  There is probably already CAN and power near where the sensor would need to be.

# todo:
- Finish schematics
- Finish firmware
- Finish FRC Java library

# Features:
- CAN interface to RoboRio (1Mbit, CAN-FD mode not supported)
- Pass-through CAN pins on 2 connectors
- Latching connector for CAN and GPIO.
- 4 GPIO pins (each connector has 5V,GND,GPIO,GPIO).
- digital input/output on all 4 pins
- 10-bit ADC input on all 4 pins
- 8-bit DAC+OPAMP output on 2 pins
- AB encoder on 2 pins
- Neopixel output on 1 pin.  Supports 6 LED groups, and each group is a programmable number of LEDs.
- PWM output on all 4 pins (TBD)???
- PWM input measurement on all 4 pins (TBD)???
- 5V @ 1A output power
- 6V to 25V input voltage (very low current draw, except for 5V output power)
- Reverse voltage protection on CAN and GPIO connectors.
- Can be powered by USB or CAN connector (but don't do both, because it might back-power the USB port).
  
  
# NeoPixel
- Uses an off-the-shelf neopixel array (WS2812B 5V leds) [Strip_of_leds](https://www.amazon.com/dp/B09PBHJG6G) [ring_of_leds](https://www.amazon.com/dp/B08GPWVD57)
- Takes over GPIO#0
- You MUST pay attention to the current draw of the string.  The entire IO_CAN is limited to 1A, and if you draw much more than that, the fuse will blow, and everything will stop working.  So make sure that the length of string can't draw too much power.
- Possibly add an external power connection to power the neopixel array.
- Color choices:
  - 0x00-0x3f: Black-to-White solid color choices (4 values per R,G,B)
  - 0x80-0xff: Single white LED on (can be set to match the rotary encoder)
  - Rainbow
  - Moving Rainbow

# Files included:
- UsbJoy_d12_a4_r-rev2/: Kicad layout files, and gerbers
- SmartButton-rev2/: Kicad layout files, and gerbers
- UsbJoy_bill_of_materials.csv: spreadsheets with all of the parts to purchase and quantities and ordering links, pcb, buttons, cables, other stuff...
- mechanics/: openscad & STL files for a couple of mounts for UsbJoy using the SmartButton, rotary dial, 
- software/SmartButton_firmware/:
- software/UsbJoy_firmware/: firmware for the Atmega32U4 microcontroller
- software/UsbJoy_pic_firmware/: firmware 
- software/program_pic/: small application to program the pic16f1778 on UsbJoy, and pic15f15213 on SmartButton

# Manufacturing suggestions:
- [oshpark.com](https://oshpark.com/) has wonderful pricing for qty3 of 2and4 layer boards and ships in 10 calendar days for almost everything (and you can pay more to get it earlier, or pay for faster shipping)
  - [UsbJoy_R2](https://oshpark.com/shared_projects/QPZCB1ZT) board order $20/3 boards
  - [SmartButton_R2](https://oshpark.com/shared_projects/OV1uAkH7) board order $1.70/3 boards ($6.80/12 boards)
- A 3d-printer can make the UsbJoy board attach directly to the screw threads of one of the buttons.  A couple of sample mount's source files are [here](https://makerworld.com/en/models/1602536-usbjoy-controller-mount-30mm-arcade-button).
- An 3d printed [encoder spring](https://makerworld.com/en/models/1602721-rotary-encoder-rotary-friction-spring) makes the encoder much better for not spinning the robot wildly.
- An [acrylic wheel](https://makerworld.com/en/models/1605249-encoder-finger-wheel) with finger holes for the encoder is very useful.
- Testing for button functionality can be done in windows with the built-in "USB game controllers"->properties window to see the buttons that get pressed. NEED A PICTURE HERE!
- You can extract a 3d model of the boards from kicad to use in your 3d cad system.  Kicad: File->Export->STEP
- digikey has all of the electrical components necessary (except for the LED and Arduino board).
- 3x UsbJoy parts kit
- 12x SmartButton parts kit
- The arduino pro micro has a 0.5A fuse on it.  The bottom side of the board will NOT be accessible.  If that 0.5A fuse is on the bottom side, and you have over current, then your boards will not be salvageable.  So get a version that has all the components on the top side.
- The discrete components are 0603 packages for easier soldering.
- Get some [illuminated magnifying glasses](https://www.amazon.com/YOCTOSUN-Magnifying-Rechargeable-1-5X-3-5X-Magnifier/dp/B0D8J3TYL3) for ease of soldering of small components. 
- Solder all of the SMT components before any thru-hole components.  Required for both boards, because some of the thru-hole soldering will make SMT parts unreachable.

# How to program Arduino Pro Micro's Atmega32U4 (the USB device):
- Run the [Arduino GUI](https://www.arduino.cc/en/software/) version 1.8.19 (or newer than May 2025).
- Load the *.ino file
- Set up the board to be "Arduino Leonardo".
- Plug in the board into USB, then select the right Com port in the Arduino GUI.
- "Upload" <img width="28" height="27" alt="image" src="https://github.com/user-attachments/assets/270d445c-2d22-4503-96b8-3c62cd0f49b2" />
 from the Arduino GUI.
- Programming does not clear the EEPROM, so any settings should remain unaffected.

# How to program pic16f1778 on UsbJoy using PICKIT5 programmer:
- Disconnect the last 2 buttons (Yellow and white next to each other)
- type "q1" on the uart to disable the 7,10,11 button communications
- Run the [Microchip IDE](https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide)
- Load the project
- Compile the project for production
- Use one of the current PIC programmers (pickit5 is probably preferred), and plug it into the 5-pin holes on the bottom side of the board.  And have the IDE program the device.
- type "q0" on the uart to re-enable the last 2 button functionality
# How to program pic16f1778 on UsbJoy using UsbJoy's USB connection:
- Disconnect buttons 7,10,11 (Green, White, Yellow) - they will interfere with the programming.
- Connect UsbJoy to the Windows PC.
- Figure out what com port (use Windows Device Manager) UsbJoy is using.
- Run "program_pic.exe usbjoy [filename].hex COM[#]"
- Press and hold the button next to the rotary connector for the full programming time (about 15 seconds).  If you forget this step, then you will receive a message "Error: couldn't find expected PIC CPU".
- Wait for programming to complete.
- Release the button.
- You can now reconnect the disconnected buttons.

# How to program pic16f15213 on SmartButton using PICKIT5:
- Run the [Microchip IDE](https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide)
- Load the project
- Compile the project for production
- Make sure the SmartButton isn't pressed, Connect the 3-pin cable to the UsbJoy (for 5V power), plug the pickit5 into the 5-pin holes on the SmartButton board.  And have the IDE program the device.
# How to program pic16f15213 on SmartButton using UsbJoy's USB interface:
- Connect 3+3+3pin cable ends into UsbJoy (match yellow J12,white J11,green J8 connectors), connect 5-pin cable end into SmartButton.
- Run "program_pic sb smartbutton.hex COM[#]".
- Quick Test: While plugged into the 5-pin cable, you should see the led in the button flicker VERY VERY dim.
- Quick Test: While plugged into the 5-pin cable, if you press the button, the LED should light up.
- You will need to create a cable to connect J8,J11,J12 to a 5-pin header.  It is probably easiest and more reliable to make an 3+3+3-pin to 5-pin XH only cable, and then solder the B5B-XH header to a 0.1" header.
 - pin1: MCLRn -> Red/Green/J8.2
 - pin2: 5V -> White/White/J11.1
 - pin3: GND -> Blue/Yellow/J12.3
 - pin4: PGD -> Blue/Yellow/J12.2
 - pin5: PGC -> White/White/J11.2
 - <img width="676" height="497" alt="image" src="https://github.com/user-attachments/assets/cd0be9e9-08c2-4cd5-b7af-086c0df9257c" />
 - <img width="385" height="551" alt="image" src="https://github.com/user-attachments/assets/d54e4672-3bee-4afb-915b-5ecb00b70a6f" />



# How to initially configure UsbJoy:
- Connect USB to a PC.
- Open a terminal program (I like [MobaXterm](https://mobaxterm.mobatek.net/)), and connect to the correct com port (use windows device manager, and find the 'leonardo' port listed) (linux: 'dmesg' to find the correct '/dev/ttyUSB#' when you plug into USB).
- Any baud rate is fine, because it is a virtual serial port.
- 'h'+enter to get the help menu
- All settings commands are "letter+hex_number+enter".
- Important EEPROM settings to run:
  - 'u#" to set the upper 4 bits for the virtual buttons to uniquely identify this particular board (saved to EEPROM)
  - "m#val" to set the SmartLed DEFAULT setting for that port (not for the SmartLed itself) to the desired LED pattern (and toggle functionality) (saved to EEPROM)
  - "n#" to set the number of neopixel leds in the string - set to "n0" to indicate that there isn't a neopixel string.
  - "v#" to set the power-on setting for the Green ports - probably want them always on "vF" unless they are being used in some interesting way.
  - "V#" to set the power-on DEFAULT for the Green ports (saved to EEPROM)

# Future enhancements
- Add a 5-pin direct programming header for connecting to SmartButton (no need for weird programming cable).
- Move QR code to top side of board.

# History of major changes
- 8/8/2025: Added RZ axis to have the full resolution of the 2400/rev encoder when used with FRC DriverStation that only has 200 values per axis.
- 7/16/2025: Added MPLAB project files.
- 7/16/2025: SmartButton pic programming working over USB
- 7/16/2025: Added PDF of schematics.
- 7/15/2025: Uploaded 3d and lasercut objects to makerworld.com

Neal Palmer,
**FRC team #2984 Vikings Robotics,**
La Jolla, CA, USA
  
