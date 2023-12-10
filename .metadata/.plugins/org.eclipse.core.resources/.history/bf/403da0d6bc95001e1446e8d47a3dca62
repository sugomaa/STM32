#include "main.h"

void init_tim_gpio(void){

RCC -> AHBENR |= RCC_AHBENR_GPIOEEN;
//TIM1_CH3, PE13, AF2 (alternate function)

GPIOE -> MODER |= GPIO_MODER_MODER9_1;
GPIOE -> MODER &=~ GPIO_MODER_MODER9_0;

GPIOE -> MODER &=~ GPIO_MODER_MODER13_1;
GPIOE -> MODER |= GPIO_MODER_MODER13_0;

//GPIOE -> AFR[1] |= GPIO_AFRH_AFRH2;
//GPIOE -> AFR[1] |= AFR_AFRH_AFRH5 & (1<<2);
GPIOE -> AFR[1] |= (1<<5);

GPIOE -> OSPEEDR |= GPIO_OSPEEDER_OSPEEDR9_0;
GPIOE -> OSPEEDR |= GPIO_OSPEEDER_OSPEEDR9_1;
}

void init_tim(int dutty)
{
//PWM 1 MODE
RCC -> APB2ENR |= RCC_APB2ENR_TIM1EN;
TIM1 -> CCMR1 |= TIM_CCMR1_OC1PE;

//Prescaler 0x0F
TIM1 -> PSC = 0x0F;

//

TIM1 -> CCMR1 &=~ TIM_CCMR1_OC1M_0;
TIM1 -> CCMR1 |= TIM_CCMR1_OC1M_1;
TIM1 -> CCMR1 |= TIM_CCMR1_OC1M_2;
TIM1 -> CCMR1 &=~ TIM_CCMR1_OC1M_3;

//Frequency register: ARR, where period is 1000
TIM1 -> ARR = 100;

//Duty cycle
TIM1 -> CCR1 = dutty;

//
TIM1 -> BDTR |= TIM_BDTR_MOE;
TIM1 -> CCER |= TIM_CCER_CC1E;
TIM1 -> CR1 |= TIM_CR1_CEN;
}

void EXTI0_config(void){
RCC -> APB2ENR |= RCC_APB2ENR_SYSCFGEN;
SYSCFG -> EXTICR[0] &=~ SYSCFG_EXTICR1_EXTI0;
SYSCFG -> EXTICR[0] |= SYSCFG_EXTICR1_EXTI0_PA;

//Trigger on rising edge
EXTI -> RTSR &=~ EXTI_RTSR_TR0;
EXTI -> FTSR |= EXTI_FTSR_TR0;

//Enable interrupt
EXTI -> IMR |= EXTI_IMR_MR0;

//NVIC
NVIC_EnableIRQ(EXTI0_IRQn);
}

void EXTI0_IRQHandler(void){
if (EXTI -> PR & EXTI_PR_PR0){
//clear interrupt
EXTI -> PR |= EXTI_PR_PR0;
TIM1 -> CCR1 += TIM1 -> ARR/10;
}
}


int main(void)
{

EXTI0_config();
init_tim_gpio();



while (1)
{

int b = 0;
while (b<100){

init_tim(b);
b++;

for (int i=0; i<5000; i++);
//GPIOE -> BSRR |= GPIO_BSRR_BS_13;
}



if (b>=100){
while(b>0){

init_tim(b);
b--;

for (int i=0; i<5000; i++);
//GPIOE -> BSRR |= GPIO_BSRR_BR_13;
}
}



}

}
