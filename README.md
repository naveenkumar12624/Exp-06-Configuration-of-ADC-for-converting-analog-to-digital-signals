# Exp-06-Configuration-of-ADC-for-converting-analog-to-digital-signals


## Name :	S.Naveen Kumar
## Roll no: 212221240033
  
  
## Aim: To configure internal ADC for   LPC2148 ARM 7 and write a code for displaying the values varying from 0v to 3.3v to its equivalent digital values 
## Components required: Proteus ISIS professional suite, Kiel μ vision 5 Development environment 
 
 ![image](https://user-images.githubusercontent.com/36288975/198947663-2d75f694-880a-4bc0-be67-8c2d4125fdb6.png)

Figure-01 ADC pins in LPC2148 ARM 7 controller 


 ## Theory 
Analog to Digital Converter (ADC) is used to convert analog signal into digital form. LPC2148 has two inbuilt 10-bit ADC i.e. ADC0 & ADC1.
•	ADC0 has 6 channels &ADC1 has 8 channels.
•	Hence, we can connect 6 distinct types of input analog signals to ADC0 and 8 distinct types of input analog signals to ADC1.
•	ADCs in LPC2148 use Successive Approximation technique to convert analog signal into digital form.
•	This Successive Approximation process requires a clock less than or equal to 4.5 MHz. We can adjust this clock using clock divider settings.
•	Both ADCs in LCP2148 convert analog signals in the range of 0V to VREF (typically 3V; not to exceed VDDA voltage level).
AD0.1:4, AD0.6:7 & AD1.7:0 (Analog Inputs)
These are Analog input pins of ADC. If ADC is used, signal level on analog pins must not be above the level of VDDA; otherwise, ADC readings will be invalid. If ADC is not used, then the pins can be used as 5V tolerant digital I/O pins.
VREF (Voltage Reference)
Provide Voltage Reference for ADC.
VDDA& VSSA (Analog Power and Ground)
These are the power and ground pins for ADC. These should be same as VDD & VSS.
 
Let’s see the ADC registers which are used to control and monitors the ADC operation.
Here, we will see ADC0 registers and their configurations. ADC1 has similar registers and can be configured in a similar manner.
 
## ADC0 Registers 
1.  AD0CR (ADC0 Control Register)
•	AD0CR is a 32-bit register.
•	This register must be written to select the operating mode before A/D conversion can occur.
•	It is used for selecting channel of ADC, clock frequency for ADC, number of clocks or number of bits in result, start of conversion and few other parameters.
 
 ![image](https://user-images.githubusercontent.com/36288975/198947597-f7dfb87b-3dea-4b93-a0cd-dc214762f354.png)

 Figure-02 AD0CR (ADC0 Control Register)
•	Bits 7:0 – SEL
These bits select ADC0 channel as analog input. In software-controlled mode, only one of these bits should be 1.e.g. bit 7 (10000000) selects AD0.7 channel as analog input.
•	Bits 15:8 – CLKDIV
The APB(ARM Peripheral Bus)clock is divided by this value plus one, to produce the clock for ADC.
This clock should be less than or equal to 4.5MHz.
•	Bit 16 – BURST
0 = Conversions are software controlled and require 11 clocks
1 = In Burst mode ADC does repeated conversions at the rate selected by the CLKS field for the analog inputs selected by SEL field. It can be terminated by clearing this bit, but the conversion that is in progress will be completed.
When Burst = 1, the START bits must be 000, otherwise the conversions will not start.
•	Bits 19:17 – CLKS
Selects the number of clocks used for each conversion in burst mode and the number of bits of accuracy of Result bits of AD0DR.
e.g. 000 uses 11 clocks for each conversion and provide 10 bits of result in corresponding ADDR register.
000 = 11 clocks / 10 bits
001 = 10 clocks / 9 bits
010 = 9 clocks / 8 bits
011 = 8 clocks / 7 bits
100 = 7 clocks / 6 bits
101 = 6 clocks / 5 bits
110 = 5 clocks / 4 bits
111 = 4 clocks / 3 bits
•	Bit 20 – RESERVED
•	Bit 21 – PDN
0 = ADC is in Power Down mode
1 = ADC is operational
•	Bit 23:22 – RESERVED
•	Bit 26:24 – START
When BURST bit is 0, these bits control whether and when A/D conversion is started
000 = No start (Should be used when clearing PDN to 0)
001 = Start conversion now
010 = Start conversion when edge selected by bit 27 of this register occurs on CAP0.2/MAT0.2 pin
011= Start conversion when edge selected by bit 27 of this register occurs on CAP0.0/MAT0.0 pin
100 = Start conversion when edge selected by bit 27 of this register occurs on MAT0.1 pin
101 = Start conversion when edge selected by bit 27 of this register occurs on MAT0.3 pin
110 = Start conversion when edge selected by bit 27 of this register occurs on MAT1.0 pin
111 = Start conversion when edge selected by bit 27 of this register occurs on MAT1.1 pin
•	Bit 27 – EDGE
This bit is significant only when the Start field contains 010-111. In these cases,
0 = Start conversion on a rising edge on the selected CAP/MAT signal
1 = Start conversion on a falling edge on the selected CAP/MAT signal
•	Bit 31:28 – RESERVED
2.  AD0GDR (ADC0 Global Data Register)
•	AD0GDR is a 32-bit register.
•	This register contains the ADC’s DONE bit and the result of the most recent A/D conversion.
 
 ![image](https://user-images.githubusercontent.com/36288975/198947519-3a019070-02b1-413f-af08-4536c367981d.png)

Figure-03 AD0GDR (ADC0 Global Data Register)
•	Bit 5:0 – RESERVED
•	Bits 15:6 – RESULT
When DONE bit is set to 1, this field contains 10-bit ADC result that has a value in the range of 0 (less than or equal to VSSA) to 1023 (greater than or equal to VREF).
•	Bit 23:16 – RESERVED
•	Bits 26:24 – CHN
These bits contain the channel from which ADC value is read.
e.g. 000 identifies that the RESULT field contains ADC value of channel 0.
•	Bit 29:27 – RESERVED
•	Bit 30 – Overrun
This bit is set to 1 in burst mode if the result of one or more conversions is lost and overwritten before the conversion that produced the result in the RESULT bits.
This bit is cleared by reading this register.
•	Bit 31 – DONE
This bit is set to 1 when an A/D conversion completes. It is cleared when this register is read and when the AD0CR is written.
If AD0CR is written while a conversion is still in progress, this bit is set and new conversion is started.
 
3.  ADGSR (A/D Global Start Register)
•	ADGSR is a 32-bit register.
•	Software can write to this register to simultaneously start conversions on both ADC.

 ![image](https://user-images.githubusercontent.com/36288975/198947463-02050b1b-139a-46a3-b264-e07704f462bc.png)

  Figure-04 ADGSR (A/D Global Start Register)
•	BURST (Bit 16), START (Bit <26:24>) & EDGE (Bit 27)
These bits have same function as in the individual ADC control registers i.e. AD0CR & AD1CR. Only difference is that we can use these function for both ADC commonly from this register.
 
4.  AD0STAT (ADC0 Status Register)
•	AD0STAT is a 32-bit register.
•	It allows checking of status of all the A/D channels simultaneously.

![image](https://user-images.githubusercontent.com/36288975/198947435-c0d0d558-615b-4f1b-8d29-5ea985e02231.png)

  Figure-05 AD0STAT (ADC0 Status Register)
•	Bit 7:0 – DONE7:DONE0
These bits reflect the DONE status flag from the result registers for A/D channel 7 - channel 0.
•	Bit 15:8 – OVERRUN7:OVERRUN0
These bits reflect the OVERRUN status flag from the result registers for A/D channel 7 - channel 0.
•	Bit 16 – ADINT
This bit is 1 when any of the individual A/D channel DONE flags is asserted and enables ADC interrupt if any of interrupt is enabled in AD0INTEN register.
•	Bit 31:17 – RESERVED
 
5.  AD0INTEN (ADC0 Interrupt Enable)
•	AD0INTEN is a 32-bit register.
•	It allows control over which channels generate an interrupt when conversion is completed.


![image](https://user-images.githubusercontent.com/36288975/198947409-b9335191-fd9e-45c3-9d23-820e3df72488.png)

  Figure-06 AD0INTEN (ADC0 Interrupt Enable)
•	Bit 0 – ADINTEN0
0 = Completion of a A/D conversion on ADC channel 0 will not generate an interrupt
1 = Completion of a conversion on ADC channel 0 will generate an interrupt
•	Remaining ADINTEN bits have similar description as given for ADINTEN0.
•	Bit 8 – ADGINTEN
0 = Only the individual ADC channels enabled by ADINTEN7:0 will generate interrupts
1 = Only the global DONE flag in A/D Data Register is enabled to generate an interrupt
 
6.  AD0DR0-AD0DR7 (ADC0 Data Registers)
•	These are 32-bit registers.
•	They hold the result when A/D conversion is completed.
•	They also include flags that indicate when a conversion has been completed and when a conversion overrun has occurred.


![image](https://user-images.githubusercontent.com/36288975/198947370-c2a27bb7-d2f7-47eb-8184-1bdeae95511d.png)

  Figure-07 AD0 Data Registers Structure
•	Bit 5:0 – RESERVED
•	Bits 15:6 – RESULT
When DONE bit is set to 1, this field contains 10-bit ADC result that has a value in the range of 0 (less than or equal to VSSA) to 1023 (greater than or equal to VREF).
•	Bit 29:16 – RESERVED
•	Bit 30 – Overrun
This bit is set to 1 in burst mode if the result of one or more conversions is lost and overwritten before the conversion that produced the result in the RESULT bits.
This bit is cleared by reading this register.
•	Bit 31 – DONE
This bit is set to 1 when an A/D conversion completes. It is cleared when this register is read.
 


Procedure:
Steps for Analog to Digital Conversion
1.	Configure the ADxCR (ADC Control Register) according to the need of application.
2.	Start ADC conversion by writing appropriate value to START bits in ADxCR. (Example, writing 001 to START bits of the register 26:24, conversion is started immediately).
3.	Monitor the DONE bit (bit number 31) of the corresponding ADxDRy (ADC Data Register) till it changes from 0 to 1. This signals completion of conversion. We can also monitor DONE bit of ADGSR or the DONE bit corresponding to the ADC channel in the ADCxSTAT register.
4.	Read the ADC result from the corresponding ADC Data Register.
ADxDRy. E.g. AD0DR1 contains ADC result of channel 1 of ADC0.

 ![image](https://user-images.githubusercontent.com/36288975/198947241-e5190f5c-0a23-4026-ae2c-722c43e51e6c.png)

Figure -08 Circuit diagram of interfacing an POT with ADC input pin 

## Kiel - Program 
 ```
 #include<lpc214x.h>
#include "LCD.h"
#include "ADC.h"
int main()
{
	IO1DIR=0xffffffff;
	IO0DIR=0x00000000;
	PINSEL0=0x0300;
	VPBDIV=0x02;
	lcd_init();
	show(" ADC Value: ");
	while(1){
		cmd(0x8b);
		val=adc(0,6);
		dat((val/1000)+48);
		dat(((val/100)%10)+48);
		dat(((val/10)%10)+48);
		dat((val%10)+48);
	}
}
```
## Tabulations and graph 
![o1](https://user-images.githubusercontent.com/94165336/200356280-63426d44-9794-4ca3-9a11-8b0220ddc999.png)
![o4](https://user-images.githubusercontent.com/94165336/200356309-4d880bda-41c9-4710-ba83-75cd15f0003d.png)




Result :
Configuring an ADC and the input values are displayed on LCD screen 

Output screen shots :
![pic1](https://user-images.githubusercontent.com/94165336/200356087-747b0b28-8fa8-4311-9d85-a5df6737797c.png)
![pic2](https://user-images.githubusercontent.com/94165336/200356109-6171286f-d712-4bbb-b84c-638da7bf8d0f.png)
![pic3](https://user-images.githubusercontent.com/94165336/200356145-4596d402-665d-42da-9ee3-a36bb6003fbb.png)





