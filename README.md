# E226L3 - Interfacing MSP with Switch & LED
### Components
- MSP432P401R 32-Bit ARM Cortex-M4F MCU
- CodeComposer V8

**Objective 1** : Sequencing colored LEDs using a pushbutton switch

**Objective 2** : Achieve objective 1 with a timer module

#### Part 1
- Upon startup or program reset, all LEDs are OFF.
- The first push of the button lights the red LED and it stays on. 
- On the second push of the button, the red LED turns off and the green LED turns on (and stays on).  
- After the third push of the button, the green LED turns off and the blue LED turns on (and stays on).
- For each subsequent button push, the LEDs repeat the sequence described above (one LED on at a time, each turning on and off in response to a button press). If the button is held down, it does not change the LED color.
- Switch bounce was handeled with a delay function with **__delay_cycles( )** 

#### Execution
This was achieve by using bitwise operations.
In table 1, it shows which pins needed to be turned on in order to achieve the desired color. For one color to shift to another, it needs a mask. The mask needed to turn the previous bit off and the desired bit on. See table 2 for bitwise operation. In code, this would be
expressed by the XOR operator, ' ^= ' .

**Table 1. XOR Operator**

Color | 8-Bit    | Mask| Result| Color change
---   | ---   | ---|---|--
Red   | 0001  | 0011 |0010| Red > Green
Green | 0010  |0110 |0100| Green > Blue
Blue  | 0100  |0101 |0001| Blue > Red

### Part 2
- Similar to part 1, but work with the MSP timer.
- So, when the button is held, the lights should change. In comparison to previously staying on the current color. 

#### Execution
- Using **SysTick**
````#include "msp.h"

int main(void) {
    /* initialize P2.0 for red LED */
    P2->SEL1 &= ~1;             /* configure P2.0 as simple I/O */
    P2->SEL0 &= ~1;
    P2->DIR |= 1;               /* P2.0 set as output */

    /* Configure SysTick */
    SysTick->LOAD = 3000000-1;  /* reload reg. with 3,000,000-1 */
    SysTick->VAL = 0;           /* clear current value register */
    SysTick->CTRL = 5;          /* enable it, no interrupt, use master clock */

    while (1) {
        if (SysTick->CTRL & 0x10000)    /* if COUNTFLAG is set */
            P2->OUT ^= 1;               /* toggle red LED */
    }
}
````

