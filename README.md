# hallow
this is the main function ,if you want to find the other code, please look the branch
#include "common.h"
#include "key.h"
#include "fft.h"
#include "usart3.h"

/*********************************************************************************
********************** STM32F407应用开发板(高配版)*************************
**********************************************************************************
* 文件名称: DSP FFT使用                                                         *
* 文件简述：                                                                     *
* 创建日期：2020.8.21                                                          *
* 版    本：V1.0                                                                 *
* 作    者：近视未看清                                                               *
* 说    明：将时域序列信号FFT后幅值、频率、相位通过串口发送到PC                         * 
*                                     *
**********************************************************************************
*********************************************************************************/

float input[length*2];//前一个数表示实部，后一个表示虚部
float Amp[length];//存放幅值
float rate[length];//存放频率
float Phase[length];
int main(void)
{ 
	u16 i;
  NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2); //设置系统中断优先级分组2
	delay_init();		  //初始化延时函数
	KEY_Init();	      //IO初始化
	uart3_init(9600);
	FFTx4_init(0,1);//FFT初始化函数（基4）
	//生成信号序列
	for(i=0;i<length;i++)
	{
		input[i*2]=100+10*arm_sin_f32(2*PI*i/length)+30*arm_sin_f32(2*PI*i*4/length)+50*arm_cos_f32(2*PI*i*8/length);	//生成输入信号实部
		input[2*i+1]=0;//时域，虚部为零
	}
	
  while(1)
	{
		key_scan(0);
		if(keydown_data==KEY0_DATA)
		{
			all_result_x4(input, Amp, rate, Phase,1024);//计算各点幅值、频率、相位
			printf("A:       F;    P\n");
			for(i=0;i<length;i++)
			printf("%.2f: %.2f: %.2f\n",Amp[i],rate[i],Phase[i]);//向串口发送幅值、频率、相位
		}
    delay_ms(5);
	}
}
