/*********************************************************************************************/
【*】程序简介

-工程名称：基于OV5640摄像头二维码识别
-实验平台: STM32 F429 开发板 
-MDK版本：5.16
-ST固件库版本：1.5.1

【 ！】功能简介：
使用DCMI驱动OV5640摄像头，拍摄的图像直接显示在液晶屏上，通过QR-Code库解二维码，串口发送数据。



【 ！】实验操作：
连接好配套的5.0寸液晶屏，OV5640摄像头，下载程序后复位开发板即可，屏幕会将扫描框内的二维码解码并通过串口发送结果。



【*】注意事项：
OV5640与RGB LED灯引脚共用，两个设备不能同时使用。


【*】性能
条码种类： 支持常用QR-Code、EAN、UPC
扫描速度： 400 ms
扫描英文： 250 个字节
扫描中文： 90个中文字符，UTF-8编码格式(需上位机支持UTF-8编码)
扫描框：   支持自定义大小
多码扫描： 支持多个二维码放在一起扫描解码，同时输出结果 

【*】液晶屏分层格式

底层RGB565格式，顶层ARGB8888格式。
摄像头显示在底层，文字显示在顶层。
摄像头显示部分的液晶层(本例为底层)只支持RGB565格式。

/*******************************************************************************************************************/

【*】 引脚分配

摄像头：
摄像头数据接口与STM32的DCMI接口相连，控制接口与STM32的IIC相连。
		HREF	<--->PA4
		VSYNC	<--->PI5
		XCLK	<--->PA6
		PCK		<--->PA8
		RST		<--->PG2
		PWDN	<--->PG3

		SCL<--->PD12
		SDA<--->PD13
		
数据线DO0-DO7省略。



液晶屏：
液晶屏接口与STM32的LTDC接口相连，支持RGB888、565格式，
STM32直接驱动，无需外部液晶屏驱动芯片.

		/*液晶控制信号线*/		
		CLK		<--->PG7
		HSYNC	<--->PI10
		VSYNC	<--->PI9
		DE		<--->PF10
		DISP	<--->PD4
		BL		<--->PD7
		
		/*电容触摸屏信号线*/		
		RSTN	<--->PD13
		INT		<--->PD12
		SDA		<--->PH5
		SCL		<--->PH4

RGB信号线省略,本实验没有驱动触摸屏，详看触摸画板实验。


SDRAM （W9825G6KH 32M 字节）：
SDRAM芯片的接口与STM32的FMC相连。
		/*控制信号线*/
		CS	<--->PH6
		BA0	<--->PG4
		BA1	<--->PG5
		WE	<--->PC0
		CS	<--->PH6
		RAS	<--->PF11
		CAS	<--->PG15
		CLK	<--->PG8
		CKE	<--->PH7
		UDQM<--->PE1
		LDQM<--->PE0
		
地址和数据信号线省略，本连接的SDRAM基地址为 (0xD0000000)，结束地址为(0xD2000000),大小为32M字节

FLASH(W25Q128)：
FLASH芯片的QSPI接口与STM32的QSPI相连。
		CLK	<--->PB2
		IO0 <--->PF9
		IO1 <--->PF8
        IO2 <--->PF7
        IO3 <--->PF6
		CS	<--->PB6

串口(TTL-USB TO USART)：
CH340的收发引脚与STM32的发收引脚相连。
	RX<--->PA9
	TX<--->PA10
												
/*****************************************************************************************************/


【*】 时钟

A.晶振：
-外部高速晶振：25MHz
-RTC晶振：32.768KHz

B.各总线运行时钟：
-系统时钟 = SYCCLK = AHB1 = 216MHz
-APB2 = 108MHz 
-APB1 = 54MHz

C.浮点运算单元：
  使用

/*********************************************************************************************/