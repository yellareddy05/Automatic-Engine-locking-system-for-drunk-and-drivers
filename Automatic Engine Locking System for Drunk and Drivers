#include<reg52.h>
#include<intrins.h>
#define lcd P0

sbit rs=P1^5; 
sbit rw=P1^6;
sbit en=P1^7;
sbit lcd_bsy = P0^7;

sbit CLK = P2^2;
sbit DOUT= P2^3;
sbit DIN = P2^4;
sbit CS  = P2^0;


sbit mtr = P1^0;

void init_lcd(void);
void cmd_lcd(unsigned char);
void lcd_data(unsigned char);
void display_lcd(unsigned char *);
void LCD_Busy_Chk(void);

void delay_ms(unsigned int);
void integer_lcd(int);
void float_lcd(float f);
float adc_convert (void);
unsigned char byte_write_read(unsigned char);
void select_channel(void);

unsigned char channel,A,C;
int f,f1,x,y;int i=0;
/**************************/
void main(void)
{
TMOD = 0x20;
SCON = 0x50;
TH1 = 0xFD;
TR1 = 1;
IE = 0x90;

init_lcd();
delay_ms(10);

cmd_lcd(0x01);
cmd_lcd(0x80);
display_lcd("AUTOMATIC ENGINE");
cmd_lcd(0xC0);
display_lcd("LOCK-DRUNK&DRIVE");
delay_ms(500);
cmd_lcd(0x01);
while(1)
{

delay_ms(30);
cmd_lcd(0x01);
cmd_lcd(0x80);
display_lcd("ALCHOL:");
cmd_lcd(0x87);
channel=0; 
f=adc_convert();
integer_lcd(f);
display_lcd("%");
delay_ms(10);
if(f<150)
{
mtr=1;
cmd_lcd(0xC0);
display_lcd("VEHICLE RUNNING");
}
if(f>150)
{
mtr=0;
cmd_lcd(0x01);
cmd_lcd(0x80);
display_lcd("VEHICLE STOPPED");
cmd_lcd(0xC0);
display_lcd("ALCHOL LEVEL HIGH");
}
}
}


void init_lcd(void)
{
cmd_lcd(0x30);
cmd_lcd(0x30);
cmd_lcd(0x38);
cmd_lcd(0x0c);
cmd_lcd(0x06);
cmd_lcd(0x01);
delay_ms(10);
}
void cmd_lcd(unsigned char c)
{
 LCD_Busy_Chk();

	lcd=c;
	rs=0;
	rw=0;
	en=1;
	_nop_();
	en=0;
}
void lcd_data(unsigned char c)
{
	LCD_Busy_Chk();
	
	lcd=c;
	rs=1;
	rw=0;
	en=1;
	_nop_();
	en=0;	
}
void LCD_Busy_Chk(void)
{
		lcd_bsy=1;
		rs=0;
		rw=1;
	while(lcd_bsy==1)
		{
		en=0;
		_nop_();
	    _nop_();
		_nop_();
	    _nop_();
		en=1;
		}
}
void display_lcd(unsigned char *s)
{
while(*s)
lcd_data(*s++);
}
float adc_convert (void)
{
unsigned char byte0,byte1,byte2;
int i;
float val;
CLK=0;
CS=1;
CS=0;
select_channel();
byte0=byte_write_read(A);
byte1=byte_write_read(C);
byte2=byte_write_read(0x00);
CS=1;
i=(byte1&0x0f);
i=((i<<8)| byte2);
val=((i*5.00)/4096)*100;
//float_lcd(val);
return val;
}
unsigned char byte_write_read (unsigned char a)
{
unsigned char c=0,mask=0x80;
do
{
	CLK=1;
	if(a&mask)
	DIN=1;
	else
	DIN=0;
	CLK=0;
	if(DOUT==1)
	c|=mask;
	mask>>=1;
}
	while(mask>0);
	return c;
}

void float_lcd(float f)
{
int n;
float temp;
n=f;
integer_lcd(n);
lcd_data('.');
temp=f-n;
if(temp>=0.00&&temp<=0.09)
lcd_data('0');
f=temp*100;
n=f;
integer_lcd(n);
}

void integer_lcd(int n)
{
unsigned char c[6];
unsigned int i=0;
if(n<0)
{
lcd_data('-');
n=-n;
}
if(n==0)
lcd_data('0');
while(n>0)
{
c[i++]=(n%10)+48;
n/=10;
}
while(i-->=1)
lcd_data(c[i]);
}
void delay_ms(unsigned int i)
{
unsigned int j;
while(i-->0)
{
for(j=0;j<500;j++);
}
}

void select_channel (void)
{
unsigned char x;
x=(channel*2)+0x60;
A = x>>4;
C = x<<4;
}

