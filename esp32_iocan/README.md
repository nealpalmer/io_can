# README for source code for ESP32-S3/C3 in io_can

# Purpose
Communicate over CAN bus to roborio.
Communicate over UART to PIC micro-controller.
Store CAN-ID in eeprom.
Communicate over USB-UART for status and CAN-ID storage to eeprom.
Program the PIC in system over USB.
CAN is not used for non-volatile memory programming at all (USB is used for ESP32, PIC, CAN-ID)

# Can messages
- Setup message (8-bytes)
-   1+1+6-bit update rate {50hz enable, 1/second enable, 50/[2to25]} : a 'status message' will be sent at this rate
-   4*4-bit type for each pin {input, output, pwm, pwm decode, neopixel, AB decode, ADC, DAC}
-   16-bit pwm period (us)
- Status message
-   29-bit CAN message header {?,?,?,?,?,?}
-   8-byte data
-     2 bytes for each input pin
- 4 bytes if a pair is in AB Decoder
- PWM decoder is 2 pairs of 2 bytes each (period length in us, and pulse length in us) The resolution may need to be different for longer periods.
- Command message
-   16-bits of data for each pin {output value, pwm 16-bit pulse width in us (2ms period), neopixel 3-bit pixelgroup/12-bit RGB value, dac 8-bit}
- System message receive
-   robot state (match time, team color, enabled}
