//***********************************************************************
//************* CDA 4630 Intro to Embedded Systems, Summer 2016 
//************* Dr. Bassem Alhalabi  
//************* Course Project
//************* By Caroline N. Desouza & Reshma Thomas 


//We rewired a remote control in order to control its movements with a joystick.
//We used an MSP430 as the microcontroller.
//This code was repurposed using skeleton code given to us for an earlier project in the
//course, which used temperature and touch sensors to detect ambient light and temperature, 
//hence the variables "temproom" and "touchroom."


#include <msp430.h>

int value=0, i=0 ;
int dump0=0, dump1=0, dump2=0;		//light was pin 1.2 turned to pin 1.0
int leftRight = 0, temproom = 0;	//pin 1.0 turned to pin 1.2 left turn = 0v right turn = 3.3v
int upDown =0, touchroom =0;		//pin 1.1  down (reverse) = 0v and up (forward) = 3.3v
int ADCReading [5];

// Function Prototypes
void fadeLED(int valuePWM);
void ConfigureAdc(void);
void getanalogvalues();


int main(void)
{
	WDTCTL = WDTPW + WDTHOLD;   //Stop WDT
	P1OUT = 0;
	P2OUT = 0;
	P1DIR = 0;
	P2DIR = 0;
	P2DIR |= ( BIT1 | BIT2 | BIT3 | BIT5);	// set bits 4, 5, 6 as outputs
	P2DIR |= BIT0;							// set bit 0 as outputs

	ConfigureAdc();

	//Reading the initial joystick values, touchroom became the controlling variable for up/down (or forward/reverse) motion on the joystick,
	//while "temproom" became the controlling variable for left/right motion on the joystick.
	   __delay_cycles(250);
	   getanalogvalues();
	    touchroom = upDown; temproom = leftRight;
	   __delay_cycles(250);


	for (;;)
	{
		//Reading all joystick values repeatedly at the beginning of the main loop
 		getanalogvalues();

		//Controlling motor direction & signaling LEDs - red means forward, blue means reverse.
		if (upDown<300)
			{
				P2OUT &= ~(BIT1 + BIT2);	//red led is on and blue is off; BIT1 = red BIT2 = blue
				P2OUT |= BIT2;			   //Motor in forward motion
			}
		else if (upDown>800)
			{
				P2OUT &= ~(BIT1 + BIT2);	//blue led is on, red is off
				P2OUT |= BIT1;				//Motor in reverse motion
			}
		else if (upDown>450 && upDown<750)
			{
				P2OUT &= ~(BIT1 + BIT2);
			}
		
		//Temperature Sensor Controlling car direction (left/right) & signaling LEDs - green is left, yellow is right.
		if (leftRight<300)
			{
				P2OUT &= ~(BIT3 + BIT5);//green is on yellow is off
				P2OUT |= BIT3;//going left
			}
		else if (leftRight>700)
			{
				P2OUT &= ~(BIT3 + BIT5);//green is off yellow is on
				P2OUT |= BIT5;//going right
			}
		else if (leftRight>450 && leftRight<750)
			{
				P2OUT &= ~(BIT3 + BIT5);
			}

	}
}


//This part of the code was given.

void ConfigureAdc(void)
{
   ADC10CTL1 = INCH_4 | CONSEQ_1; 			// A2 + A1 + A0, single sequence
   ADC10CTL0 = ADC10SHT_2 | MSC | ADC10ON;
   while (ADC10CTL1 & BUSY);
   ADC10DTC1 = 0x05; 		// 3 conversions
   ADC10AE0 |= (BIT0 | BIT1 | BIT2 | BIT3 | BIT4); 	// ADC10 option select
}

void fadeLED(int valuePWM)
{
	P1SEL |= (BIT7);  // P1.0 and P1.6 TA1/2 options
	CCR0 = 100 - 0;   // PWM Period
	CCTL1 = OUTMOD_3; // CCR1 reset/set
	CCR1 = valuePWM;  // CCR1 PWM duty cycle
	TACTL = TASSEL_2 + MC_1; // SMCLK, up mode
}

void getanalogvalues()
{
	i = 0; leftRight = 0; dump0 = 0; upDown =0; // set all analog values to zero
	
	for(i=1; i<=5 ; i++)					// read all three analog values 5 times each and average
	{
		ADC10CTL0 &= ~ENC;
		while (ADC10CTL1 & BUSY);                         //Wait while ADC is busy
		ADC10SA = (unsigned)&ADCReading[0]; 			  //RAM Address of ADC Data, must be reset every conversion
		ADC10CTL0 |= (ENC | ADC10SC);                     //Start ADC Conversion
		while (ADC10CTL1 & BUSY);		//Wait while ADC is busy
		dump0 += ADCReading[4];			//p1.0
		dump1 += ADCReading[3];			//p1.1
		dump2+= ADCReading[2];			//p1.2
                          	  	  	  	  
		// sum  all 5 reading for the three variables
		upDown += ADCReading[1];//p1.3
		leftRight += ADCReading[0]; //p1.4
	}

	upDown = upDown/5; leftRight = leftRight/5;     // Average the 5 reading for the three variables
}


#pragma vector=ADC10_VECTOR
__interrupt void ADC10_ISR(void)
{
	__bic_SR_register_on_exit(CPUOFF);
}
