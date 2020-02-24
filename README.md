# LCR-T4-2-xerox_phaser_6600_lcd
Transistor Tester modified firmware to work with Xerox Phaser 6600 LCD

transistortester_original - the original Transistor Tester firmware
trunk_modified - the modified trunk folder from transistortester_original/Software/

The most important change is in lcd-routines.c file where the following lines of code where added so that 6600 LCD is properly initialized:

    _lcd_hw_write(0x80, 0b00110000);                 
    wait10us();       
    _lcd_hw_write(0x80, 0b00110000);                 
    wait200us();
    _lcd_hw_write(0x80, 0b00110000);                 
    wait200us(); 
    _lcd_hw_write(0x80, 0b00100000);      
    wait100us();  
    lcd_command(0b00101001);     //unk1 
    wait10ms(); 
    lcd_command(0b01101010);     //unk2    
    wait100us(); 
    lcd_command(0b00101000);     
    wait200us();
    lcd_command(0b00001000);     
    wait200us();
    lcd_command(0b10000001);     
    wait200us();
    lcd_command(0b00010110);     
    wait200us();

And the original initialization lines were commented out:

//   lcd_command(CMD_SetIFOptions | MODE_8BIT);		// Add for OLED
//   lcd_command(CMD_SetIFOptions | MODE_8BIT);		// Add for OLED
//   lcd_command(CMD_SetIFOptions | MODE_8BIT | 0x0A);	// 4Bit / 2 rows / 5x7 / table3 / Add for OLED
//   lcd_command(CMD_SetDisplayAndCursor);	// Display off / no Blinking  / Add for OLED
//   lcd_command(CMD_SetEntryMode | 0x02);	// increment / no Scroll
//   lcd_command(CMD_CURSOR_HOME);		// Home Command  // Add for OLED

The Makefile in trunk_modified/default folder is adjusted for my setup.
Compiled files can also be found in trunk_modified/default folder.

The changed firmware was uploaded through an Arduino UNO as ISP as follows:
C:\Users\user\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino9/bin/avrdude -CC:\Users\user\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino9/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -Uflash:w:C:\trunk\default/TransistorTester.hex:i -U eeprom:w:C:\trunk\default/TransistorTester.eep:a

In order for 6600 LCD to work few hardware modification are required:
- D1 & D2 used in VCC line for the original display should be shortened with a wire (i.e. they are not needed), otherwise 6600 LCD will get less that required 5v to work (see images/LCR-T4(T3)NoStripGrid.JPG).
- PD4(pin number 2 on atmega) should be connected to RS LCD pin
- PD5(pin number 8 on atmega) should be connected to E LCD pin (see images/RS_E_mod.jfif how they can be connected to  LCR-T4 LCD 9 & 10 pads)
As the result the LCR-T4 LCD pads are as follows:
1 GND
2 D4
3 D5
4 (missing pad)
5 D6
6 D7
7 +5V
8 GND
9 E
10 RS
11 (missing pad)
12 (missing pad)
13 (not used)

Some useful details about this device here: https://yadi.sk/d/j7lZdKsPrwTq4Q/%D0%92%D1%81%D0%B5%20%D0%BF%D1%80%D0%BE%D1%88%D0%B8%D0%B2%D0%BA%D0%B8/LCR-T4(T3)NoStripGrid
How to compile the firmware, set fuses, and upload it here: https://vrtp.ru/index.php?showtopic=26994
CMD to set fuses:
C:\Users\user\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino9/bin/avrdude -CC:\Users\user\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino9/etc/avrdude.conf -v -patmega328p -cstk500v1 -PCOM6 -b19200 -e -Uefuse:w:0xFC:m -Uhfuse:w:0xD9:m -Ulfuse:w:0xFF:m 



