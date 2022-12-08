~~~
#include <lpc214x.h>
void delay_ms(unsigned int count)
{
  unsigned int j=0,i=0;
  for(j=0;j<count;j++)
  {
    for(i=0;i<3000;i++);
  }
}
int main() 
{
    PINSEL2 = 0x000000;  
    IO1DIR = 0xffffffff; 
    while(1)
    {
       IO1SET = 0xffffffff;     
         delay_ms(1000);
       IO1CLR = 0xffffffff;   
         delay_ms(1000);
    }
}
~~~
~~~
#include <LPC214x.h>   // define LPC2148 Header file
#define led (1<<2)     // led macro for pin 2 of port0
#define sw (1<<10)     // sw macro for pin 10 of port0
int main(void)
{
	unsigned int x;
	IO0DIR|=(~sw);   // configure P1.24 - P1.31 as input
	IO0DIR|=led;     // configure P1.16 - P1.23 as output
	while(1)
	{
		x = IOPIN0 & sw;   //save status of sw in variable x
		if(x==sw)          // if switch open
		{
			IOCLR0|=led; // LED off
		}
		else               // if switch close
		{
			IOSET0 = led;  // LED on
		}
	}
}
~~~
~~~
#include<lpc214x.h>
#include<stdint.h>
#include<stdio.h>
#include<stdlib.h>

void delay_ms(uint16_t j) // Fuction for delay in milliseconds
{
		uint16_t x,i;
		for(i=0;i<j;i++)
		{
			for(x=0; x<6000; x++);   // loop to generate 1 millisecond delay with Clk = 60MHz
		}
}

void LCD_CMD(char command)
{
		IO0PIN = ( (IO0PIN & 0xFFFF00FF) | (command<<8) );
		IO0SET = 0x00000040; // EN = 1
		IO0CLR = 0x00000030; // RS = 0,RW = 0
		delay_ms(2);
		IO0CLR = 0x00000040;
		delay_ms(5);
}

void LCD_INIT(void)
{
		IO0DIR = 0x0000FFF0;  // P0.8 TO P0.15 LCD Data. P0.4,5,6 AS RS RW and EN
		delay_ms(20);
		LCD_CMD(0x38);   // Initialize lcd
		LCD_CMD(0x0C);   //Display on cursor off
		LCD_CMD(0x06);   // Auto increment cursor
		LCD_CMD(0x01);   // Display clear
		LCD_CMD(0x80);   // First line first position
}

void LCD_STRING (char* msg)
{

		uint8_t i=0;
		while(msg[i]!=0)
		{
			IO0PIN = ( (IO0PIN & 0xFFFF00FF) | (msg[i]<<8) );
			IO0SET = 0x00000050; // RS = 1, , EN = 1
			IO0CLR = 0x00000020; // RW = 0
			delay_ms(2);
			IO0CLR = 0x00000040;  // EN =0, RS and RW unchanged (i.e. RS =1, RW =0)
			delay_ms(5);
			i++;
		}
}

void LCD_CHAR (char msg)
{
		IO0PIN = ( (IO0PIN & 0xFFFF00FF) | (msg<<8) );
		IO0SET = 0x00000050; // RS = 1, , EN = 1
		IO0CLR = 0x00000020; // RW = 0
		delay_ms(2);
		IO0CLR = 0x00000040;  // EN =0, RS and RW unchanged (i.e. RS =1, RW =0)
		delay_ms(5);
}
int main(void)
{
		LCD_INIT();
		LCD_STRING("Welcome to AIML");
		LCD_CMD(0xC0);
		LCD_STRING("212221240002");
		return 0;
~~~
~~~
#include <LPC214x.H>
unsigned char dig[]= {0x88,0xeb,0x4c,0x49,0x2b,0x19,0x18,0xcb,0x8,0x9,0xa,0x38,0x9c,0x68,0x1c,0x1e};

void delay(unsigned int count)
{
	int j=0,i=0;
	for(j=0;j<count;j++)
	{
		for(i=0;i<120;i++);
	}
}

int main(void)
{
	unsigned char count=0;
	unsigned int i=0;
	IO0DIR |= (1 << 11);
	IO0SET |= (1 << 11);
	IO0DIR |= 0x007F8000;
	while(1)
	{
		count++;
		if(count == 16)count=0;
		for(i=0;i<800;i++)
		{
			IO0CLR = 0x007f8000;
			IO0SET = (dig[count] << 15);
			delay(200);
		}
  }
}
~~~
~~~
#include <LPC213x.H>              // LPC21xx definitions                      */
char a;
void uart0_init(){
  PINSEL0 = 0x00000005;           // Enable RxD0 and TxD0                     */
  U0LCR = 0x83;                   // 8 bits, no Parity, 1 Stop bit            */
  U0DLL = 97;                     // 9600 Baud Rate @ 15MHz VPB Clock         */
  U0LCR = 0x03;                   // DLAB = 0                                 */
}
void uart0_putc(char c){
 while(!(U0LSR & 0x20)); // Wait until UART0 ready to send character  
 U0THR = c; // Send character
}
int uart0_getc (void)  {                     
  while (!(U0LSR & 0x01));
  return (U0RBR);
}
int main (void)  {                
  uart0_init();      
  while (1) {                          
  a=uart0_getc();
   uart0_putc(a);
  }                               
}
~~~
~~~
#include <lpc21xx.h> 
#define RS (1<<0)
#define RW (1<<1)
#define E (1<<2)

void LCD_command(unsigned char command);
void  delay_ms(unsigned char time);
void LCD_data(unsigned char data);
void LCD_init() ;


int main(void)
{
 //PINSEL1 = 0x00000000;  //Configure PORT0 as GPIO
 //PINSEL2 = 0X00000000;  //Configure PORT1 as GPIO
 IODIR1= 0x00780000; //Configure P1.18, P1.17, P1.16 as output(for rows and column)
 IODIR0= 0x00FF0007;  //Configure P0.23 - P0.16 as output for lcd data & P0.0,P0.1,P0.2 for lcd control lines.
 LCD_init();    //Initialize LCD 16x2
 LCD_command(0x01); 
 while(1)
   {
      IOCLR1|=(1<<19);               //Making row1 LOW
      IOSET1|=(1<<20)|(1<<21)|(1<<22); //Making rest of the rows '1'
      if(!(IOPIN1&(1<<16)))             //Scan for key press
       {
        while(!(IOPIN1&(1<<16)));
        LCD_data('1');                          
       }
      if(!(IOPIN1&(1<<17)))
       {
         while(!(IOPIN1&(1<<17)));
          LCD_data('2'); 
       }
      if(!(IOPIN1&(1<<18)))
       {
         while(!(IOPIN1&(1<<18)));
          LCD_data('3'); 
       }
      IOCLR1|=(1<<20);
      IOSET1|=(1<<21)|(1<<22)|(1<<19);
      if(!(IOPIN1&(1<<16)))
{
        while(!(IOPIN1&(1<<16)));
         LCD_data('4'); 
      }
      if(!(IOPIN1&(1<<17)))
{
        while(!(IOPIN1&(1<<17)));
         LCD_data('5'); 
     }
      if(!(IOPIN1&(1<<18)))
{
        while(!(IOPIN1&(1<<18)));
         LCD_data('6'); 
     }
      IOCLR1|=(1<<21);
      IOSET1|=(1<<22)|(1<<20)|(1<<19);
      if(!(IOPIN1&(1<<16)))
{
        while(!(IOPIN1&(1<<16)));
         LCD_data('7'); 
     }
      if(!(IOPIN1&(1<<17)))
{
       while(!(IOPIN1&(1<<17)));
        LCD_data('8'); 
    }
      if(!(IOPIN1&(1<<18)))
{
        while(!(IOPIN1&(1<<18)));
         LCD_data('9'); 
}
      IOCLR1|=(1<<22);
      IOSET1|=(1<<19)|(1<<20)|(1<<21);
      if(!(IOPIN1&(1<<16)))
{
        while(!(IOPIN1&(1<<16)));
         LCD_data('*'); 
}
      if(!(IOPIN1&(1<<17)))
{
        while(!(IOPIN1&(1<<17)));
         LCD_data('0'); 
}
      if(!(IOPIN1&(1<<18)))
{
        while(!(IOPIN1&(1<<18)));
         LCD_data('#'); 
} 
   }
}


//Function to generate software delay
//Calibrated to 1ms
void  delay_ms(unsigned char time)    
{  
 unsigned int  i, j;
 for (j=0; j<time; j++)
 {
  for(i=0; i<8002; i++)
  {
  }
}
}

void LCD_command(unsigned char command)
{
 IOCLR0 = 0xFF<<16; // Clear LCD Data lines
 IOCLR0=RS;     // RS=0 for command
 IOCLR0=RW;     // RW=0 for write
 IOSET0=command<<16; // put command on data line
 IOSET0=E;   // en=1 
 delay_ms(10) ;   // delay
 IOCLR0=E;    // en=0
}
void LCD_data(unsigned char data)
{
 IOCLR0 = 0xFF<<16; // Clear LCD Data lines
 IOSET0=RS;     // RS=1 for data
 IOCLR0=RW;     // RW=0 for write
 IOSET0= data<<16;  // put command on data line
 IOSET0=E;   //en=1 
 delay_ms(10) ;    //delay
 IOCLR0=E;   //en=0
 }

void LCD_init()
{
 LCD_command(0x38); //8bit mode and 5x8 dotes (function set)
 delay_ms(10) ;   // delay
 LCD_command(0x0c); //display on, cursor off, cursor char blinking off(display on/off)
 delay_ms(10) ;   // delay
    LCD_command(0x0e);  //cursor increment and display shift(entry mode set)
    delay_ms(10) ;   // delay
 LCD_command(0x01);  //clear lcd (clear command)
 delay_ms(10) ;   // delay
 LCD_command(0x80); 
  delay_ms(10) ;//set cursor to 0th location 1st lne
 
}
~~~
# EX-04 EXPLORATORY DATA ANALYSIS
# AIM:
To perform Exploratory Data Analysis on the given data set.

# EXPLANATION:
The primary aim with exploratory analysis is to examine the data for distribution, outliers and anomalies to direct specific testing of your hypothesis.

# ALGORITHM:
# STEP 1:
Import the required packages to perform Data Cleansing,Removing Outliers and Exploratory Data Analysis.

# STEP 2:
Use boxplot method to analyze the outliers of the given dataset.

# STEP 3:
Remove the outliers using Inter Quantile Range method.

# STEP 4:
Use Countplot method to analyze in a graphical method for categorical data.

# STEP 5:
Use displot method to represent the univariate distribution of data.

# STEP 6:
Use cross tabulation method to quantitatively analyze the relationship between multiple variables.

# STEP 7:
Use heatmap method of representation to show relationships between two variables, one plotted on each axis.

# CODE:
```
import pandas as pd
import numpy as np
import seaborn as sns
df=pd.read_csv("supermarket.csv")
df.info()
df.isnull().sum()
df.boxplot()
Q1 = df.quantile(0.25)
Q3 = df.quantile(0.75)
IQR = Q3 - Q1
df = df[~((df < (Q1 - 1.5 * IQR)) |(df > (Q3 + 1.5 * IQR))).any(axis=1)]
df.boxplot()
df["City"].value_counts()
df["Customer type"].value_counts()
df["Gender"].value_counts()
df["Product line"].value_counts()
df["Payment"].value_counts()
df["Branch"].value_counts()
sns.countplot(x="Branch",data=df)
sns.countplot(x="Gender",data=df)
sns.countplot(x="Product line",data=df)
sns.countplot(x="Payment",data=df)
sns.countplot(x="Gender",hue="Product line",data=df)
sns.countplot(x="Customer type",hue="City",data=df)
sns.displot(df[df["Branch"]=="A"]["Gender"])
sns.displot(df["Branch"])
sns.displot(df[df["Product line"]=="Electronic accessories"]["Gender"])
sns.displot(df[df["Payment"]=="Credit card"]["Branch"])
pd.crosstab(df["Gender"],df["Branch"])
pd.crosstab(df["City"],df["Customer type"])
df.corr()
sns.heatmap(df.corr(),annot=True)
```

# OUTPUT:

# EXAMINING THE DATA:
![img1](https://user-images.githubusercontent.com/93427923/162758075-29f81f79-29e5-40d0-88ad-7a30af27708a.png)

# BOXPLOT METHOD TO ANALYZE OUTLIERS:
![img2](https://user-images.githubusercontent.com/93427923/162758201-9c7dfe5a-c7ea-4467-b9b2-d37d5726013e.png)

# REMOVING OUTLIERS USING IQR METHOD:
![img3](https://user-images.githubusercontent.com/93427923/162758329-2e08357d-c01f-4752-b4b0-14424826b05b.png)

# COUNTPLOT METHOD FOR DATA ANALYSIS:
![img4 1](https://user-images.githubusercontent.com/93427923/162758438-377a5b64-bf16-4bb9-b47e-ddc9db889c45.png)
![4 2](https://user-images.githubusercontent.com/93427923/162758484-5c4c5224-004b-454d-a9fb-07476d638255.png)
![img4 3](https://user-images.githubusercontent.com/93427923/162758516-7889eeff-2843-45ec-a03e-d6ec90e54289.png)

# DISPLOT METHOD FOR DATA ANALYSIS:
![img5](https://user-images.githubusercontent.com/93427923/162758681-a5c1192e-34ef-431c-82d6-47a821eb4ec7.png)
![img5 1](https://user-images.githubusercontent.com/93427923/162758713-5920e387-ef09-43dc-81b0-44d3661c97d4.png)

# COUNTPLOT METHOD TO COMPARE TWO ENTITIES:
![img6](https://user-images.githubusercontent.com/93427923/162758888-e4907048-c0bb-49b3-af8e-95c276853349.png)

# CROSS TABULATION FOR DATA ANALYSIS:
![img7](https://user-images.githubusercontent.com/93427923/162759041-4ff0ca5d-097b-4350-a663-c68ff318d5aa.png)

# CORRELATION METHOD:
![img8](https://user-images.githubusercontent.com/93427923/162759166-7a0db8b3-8a6c-4864-be9e-9f225be5a52c.png)

# INFERENCE:
1. More customers visited the supermarket from the city Yangon.
2. The most purchased department in the supermarket is Fashion accessories.
3. Cash and E-wallet is the most preferred payment mode by the customers.
4. Maximum number of customers visited the Branch A of supermarket.
5. Male customers are highest visitors of supermarket in Branch B.

# RESULT:
Hence the given data set has undergone data cleansing and outlier removal.Later Exploratory data analysis is done to get inferences from the given data set.













