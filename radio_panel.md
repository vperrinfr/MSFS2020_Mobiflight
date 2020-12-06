#Tutorial about how to setup a Radio Panel powered by Mobiflight

I will guide you to implement 8 digit 7-segment display and Rotary Encoders to create a Radio Panel via COM1 and Nav 1.

![Pic](/pics/IMG_0734.JPG)

I will use these components:

1- [Mobiflight Connector software](https://www.mobiflight.com/en/index.html)
2- [Arduino Mega 2560](https://store.arduino.cc/arduino-mega-2560-rev3)
3- [7 segments LED module MAX7219](https://www.instructables.com/MAX7219-7-Segment-Using-Arduino/)
4- [Rotary Encoder with push button KY-40](https://opencircuit.shop/Product/Rotary-encoder-module-KY-40)

##Connection 
1- Connect 7-LED segment to Arduino card:
    VCC-5V (on top of pin #22)
    GND-GND
    DIN-#22
    CS-#24
    CLK-#26

2- Connect Rotary Encoder KY-40 to Arduino card:
    CLK-#2 (for rotary)
    DT-#3 (for rotary)
    SW-#30 (for push button)
    +-5V
    GND-GND

3- Config with Mobiflight Connector software (MF)
* Open MF software
* Click Extras/Settings
* Select MobiFlight Modules tab
* Config LED
    * Right click on Mega2560 and select Add Device/LED 7-Segment
    * Set all number as following
    * DIN-22
    * CS-24
    * CLK-26
    * Num-1 (the number of 7-LED, default is 1)
    * Set the brightness, name as required

* Config KY-40
    * Right click on Mega2560 and select Add Device/Button
    * Set Pin number: 30
    * Set Name to "Push Button"
    * Right click on Mega2560 and select Add Device/Encoder
    * Left Pin: 2
    * Right Pin: 3
    * Set Name to "Rotary Encoder"

* Click Upload and OK when you done (the popups show "Upload finished")

4- Config with Output and Input
* Output:
    * Create "COM 1 STB" (shows COM1 Standby Radio to LED)
    More detail about how to connect LED with Arduino card in this topic => [tutorial](https://www.mobiflight.com/en/tutorials/seven-segment-display.html).

    * Create "COM 1 Precondition" (to make condition when push the Pushbutton)
    Edit "COM 1 Precondition" with Custom Offset: 0x66C0 (number Zero, not character O)

##How to have 6 digits instead of five ?

Do not use generic FSUIPC offset, use that one instead.
* For COM1 Active, use offset 05C4
* For COM1 Standby, use offset 05CC
* For COM2 Active, use offset 05C8
* For COM2 Standby, use offset 05D0

In my config for COM1 Active, i have the following values
* Offset : 0x05C4
* Value Type : INT
* Size in bytes : 4
* Mask value with : 0xFFFFFFFF (<- 8 F's)
* BCD Mode : check

P.S: I had Transform box check with the value "$+10000", I changed it to "$".

* Input
    * Create "Push Button". When you push it, it will select MHz or KHz to tune freq
    * Create "MHz" to tune freq (ex: 126)
    * Create "KHz" to tune freq (ex: 07)

    * Edit "Push Button"
    Chose right Module (ex: Mega2560) and Device (ex: Push Button)
    On Press tab, set Custom Offset: 0x66C0 (number Zero, not character O) the same with COM 1 Precondition in Ouput tab
    Set value: if($=0,1,0)

Edit "MHz"
Input tab:
Chose right Module (ex: Mega2560) and Device (ex: Push Button)
Use preset according to FSUIPC Offset or Input Event ID
On Left: Radio - COM1 Standby Freq MHz (-) Decrease
On Right: Radio - COM1 Standby Freq MHz (+) Decrease
Click Use button when done

Precondition tab:
Select type Config item and chose config COM 1 Preconditon with value = 1
Click Apply when done

Edit "KHz": following edit MHz with preset or input Event ID
On Left: Radio - COM1 Standby Freq KHz (-) Decrease
On Right: Radio - COM1 Standby Freq KHz (+) Decrease

and Precondition
Select type Config item and chose config COM 1 Preconditon with value = 0

Exit from Mobiflight software and restart again. I always close and restart MF after programming.
Click Run and start FSX

When you push the Push Button, the value will change from 0 to 1 and then 0 again
When value = 1, it will activate "MHz" for you to change freq or value = 0, it will activate "GHz"

Because I have just start doing with MF, Arduino then if I have anything wrong, please let me know. I will correct soon. Tks
