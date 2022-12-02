# PS/2 keyboard to matrix converter for the TRS-80 model 3/4/4p computers

A recently aquired Aster CT-80 computer came without the keyboard. As this is 
a simple 8x8 matrix, and the PS/2 keyboard protocol is supported by the
Arduino environment, is should be simple to fix. This solution uses a
Arduino Nano to interpret the PS/2 keyboard and convert it to an address
for the MT8816 Analog Switch Array having a 16x8 matrix. The required
matrix is only 8x8, so a smaller array might also work, but the MT8816 
was easier to obtain.

Because of the similarity between the keyboard hardware of the CT-80 and 
the Tandy TRS-80 models 3, 4, and 4p, it seemed logical to try this too.

Having tested it only a model 4p, I assume the software will work as is for
the other models. The hardware is slightly different, as the 4p only makes 
the matrix available, while the model 3 and 4 also provide a ground and 5 
volt line. Whe used with the Model 4p, a proper ground and +5V have to be 
provided. Providing power via the Arduino with a USB charfer is also an 
option.

A table is used to convert ASCII to matrix coordinates and another small 
one to fix the out of order addressing in the MT8816.

The sketch attempts to convert from PS/2 to ASCII and from there to the
computer specific matrix. Quite a lot of special cases are needed to get
all characters properly shifted or unshifted.

For two TRS-80 keys the match is not exact:

- The BREAK key is mapped to the PS/2 End key. PS/2 Pause/Break is not 
regular as the make and break codes are the same.
- The CLEAR key has no proper PS/2 equivalent. The PS/2 Home key is used.
Its behaviour is to clear the screen and place the cursor in the upper left
of the screen.

 
All non-TRS-80 keys are currently unmapped. 

The sketch prints lots of debug data to the Arduino console, and a simple
command processor is included to test the MT8816 outputs. The current 
commands:

    Cnn - close crosspoint nn
    Onn - open crosspoint nn
    H   - this help
    R   - reset MT8816

### Remarks on the board

The sketch is tested with a modified v1.0 board for the CT-80. A board for the TRS-80
Model 3 , 4 and 4p has to be designed. The differences between those should be supported.
More info and testing is required.

### Remarks on version 0.8 of the firmware

More key transformations are now done with a table instead of an endless list of 'if' 
statements. These are the 'plain-special' keys. The remainder are 'shifted-special' keys
which require an implicit SHIFT or an un-SHIFT. Restructuring is prepared for these too, but
not implemented yet.

### Simple test for correct operation

The board and firmware can be tested from the Arduino IDE, right after programming, requiring
only a multi-meter:

- Open the Serial Monitor set baud rate to 115200 BAUD. The prompt 
 "PS2Keyboard to Aster CT-80 matrix V0.6" should appear.
 
- Type ? &lt;Enter&gt;. The help text should appear.

- Find pin 1 and 2 from the flat-cable header. These are at the top of the header, next to 
  the text "J3".

- Measure the resistance between the pins with an multi-meter. This should be very high, 
  out of reach of most devices.

- Type C00 &lt;Enter&gt; (One letter, two numbers)

- Measure the resistance between the pins. This should in the range of 100 ohm.

- Type O00 &lt;Enter&gt; (One letter, two numbers)

- Measure the resistance between the pins. This should be very high, out of reach of most 
  devices.

The first number is the column, the second the row. The former ranges from 0 to 7, the latter 
from 0 to F (entry in hex), but only the first eight are wired to the header.


### Arduino environment

Currently I am using Arduino IDE 1.8.19 with the following libraries:

- PS2KeyAdvanced, version 1.0.9 from Paul Carpenter
- PS2KeyMap, version 1.0.9 from Paul Carpenter
