# SSSIHL_roadshow

# Main Code:

```c
#include<stdio.h>

int main(){ 

int sum = 0, i, n; 

printf("Enter the value of n = ");

scanf("%d",&n);

for(i = 1;i <= n;i++){

sum = sum + i;

}

printf("The Sum of numbers from 1 to %d is %d\n",n,sum); return 0; }
```
# Gcc, Instruction set/Architecture,Preperation,Sum,Interactive.

cd Desktop/work/tools/openlane_working_dir/openlane docker

# Floor Planning, Synthesis, Placement, Routing, Blinking, PWM Fading.

./flow.tcl -interactive

package require openlane 0.9

run_placement

prep -design picorv32a

run_synthesis

run_floorplan

eog designs/picorv32a/runs/13-12_07-

00/results/floorplan/picorva32a.floorplan.def.png

run_placement

eog designs/picorv32a/runs/13-12_07-

00/results/placement/picorva32a.placement.def.png

run_cts

run_routing

# Snapshots regarding the above commands:

![image](https://github.com/user-attachments/assets/9e07b023-8c72-49d4-aad7-b3d894701511)

![image](https://github.com/user-attachments/assets/131beb05-5139-47e7-89f4-09123ba0e422)


<img width="960" alt="riscv" src="https://github.com/user-attachments/assets/c5b4e8af-110f-4d37-be27-2315bd0c775d" />

![Screenshot 2024-12-13 115153](https://github.com/user-attachments/assets/bae9578a-3f39-4392-a4f4-639ec5c93dd5)

![image](https://github.com/user-attachments/assets/3f798ee9-1338-4e5f-9f9d-bd36b2208ffa)

![image](https://github.com/user-attachments/assets/22579a06-1daf-44a5-ba83-88e0149ea785)

![image](https://github.com/user-attachments/assets/76b16ee0-fe09-4c87-a001-951078827b79)

![image](https://github.com/user-attachments/assets/6b943877-bc72-478f-bb95-c60c14f64a7a)

![image](https://github.com/user-attachments/assets/cd7b6834-b118-421e-99c8-b4a9c8686414)

![image](https://github.com/user-attachments/assets/9b793d30-5c23-4c59-8269-b36432282fcd)

![image](https://github.com/user-attachments/assets/85c637cc-b48c-4466-a775-8d60c1552956)

![image](https://github.com/user-attachments/assets/7beb821c-6b10-4cfc-af63-0aa7ac7b7cd7)

![16](https://github.com/user-attachments/assets/6f5eda50-b41a-41b8-83ba-8348dbf265ab)

# BLINK LED

This project demonstrates how to control an LED with a microcontroller. Below is the code for blinking the LED.

## Code
```c
#include <ch32v00x.h>
#include <debug.h>

#define BLINKY_GPIO_PORT GPIOD
#define BLINKY_GPIO_PIN GPIO_Pin_6
#define BLINKY_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void Delay_Init(void);
void Delay_Ms(uint32_t n);

int main(void)
{
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
    SystemCoreClockUpdate();
    Delay_Init();

    GPIO_InitTypeDef GPIO_InitStructure = {0};

    BLINKY_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = BLINKY_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(BLINKY_GPIO_PORT, &GPIO_InitStructure);

    uint8_t ledState = 0;
    while (1)
    {
        GPIO_WriteBit(BLINKY_GPIO_PORT, BLINKY_GPIO_PIN, ledState);
        ledState ^= 1; // invert for the next run
        Delay_Ms(100);
    }
}

void NMI_Handler(void) {}
void HardFault_Handler(void)
{
    while (1)
    {
    }
}
```

# FADING LED

This project demonstrates how to generate a PWM signal using TIM2 in STM32.

## Code
```c
#include "debug.h" 
#define TIME_PERIOD 1000
#define PRESC       0
#define PULSE       632
#define STEP_SIZE   10
volatile u16 val;
volatile u8 dir;

void TIM1_PWMOut_Init(void){
    GPIO_InitTypeDef GPIO_InitStructure={0};
    TIM_OCInitTypeDef TIM_OCInitStructure={0};
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure={0};
    //RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOC | RCC_APB2Periph_TIM1, ENABLE );
    RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOD | RCC_APB2Periph_AFIO, ENABLE );
    RCC_APB1PeriphClockCmd( RCC_APB1Periph_TIM2, ENABLE );
    GPIO_PinRemapConfig(GPIO_FullRemap_TIM2, ENABLE);
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init( GPIOD, &GPIO_InitStructure);
    TIM_TimeBaseInitStructure.TIM_Period = TIME_PERIOD;
    TIM_TimeBaseInitStructure.TIM_Prescaler = PRESC;
    TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
    TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit( TIM2, &TIM_TimeBaseInitStructure);
    TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_PWM2;
    TIM_OCInitStructure.TIM_OutputState = TIM_OutputState_Enable;
    TIM_OCInitStructure.TIM_Pulse = PULSE;
    TIM_OCInitStructure.TIM_OCPolarity = TIM_OCPolarity_High;
    TIM_OC3Init( TIM2, &TIM_OCInitStructure );
    TIM_CtrlPWMOutputs(TIM2, ENABLE );
    //TIM_OC3PreloadConfig( TIM1, TIM_OCPreload_Disable );
    //TIM_ARRPreloadConfig( TIM1, ENABLE );
    TIM_Cmd( TIM2, ENABLE );
}

int main(void)
{
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
    SystemCoreClockUpdate();
    Delay_Init();
    TIM1_PWMOut_Init();
    val = 0;
    dir = 0;
    // Loop
    while(1){
        val += (dir) ? -STEP_SIZE : STEP_SIZE;
        TIM_SetCompare3(TIM2, val);
        dir ^= (val == 1000 || val == 0) ? 1 : 0;
        Delay_Ms(15);
    }
}
```
