while dongüsünde yanıp sönen led interrupt ile değiştiriliyor.

```c
#include "stm32f4xx.h"

int led=14;

void EXTI0_IRQHandler(){
	EXTI->PR |= EXTI_PR_PR0;
	
	GPIOD->ODR &= ~(1UL<<led);
	
	if(led==14){
		led=15;
	}else{
		led=14;
	}
	GPIOD->ODR |= (1UL<<led);
}


int main(){
	int i;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN |	RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0;

	EXTI->IMR |= EXTI_IMR_IM0;
	EXTI->RTSR |= EXTI_RTSR_TR0;
	
	SYSCFG->EXTICR[0] &= ~SYSCFG_EXTICR1_EXTI0;
	
	NVIC_EnableIRQ(EXTI0_IRQn);
	
	
	while(1){
		GPIOD->ODR |= (1UL<<led);
		for(i=0;i<10000000;i++);
		GPIOD->ODR &= ~(1UL<<led);
		for(i=0;i<2000000;i++);
		
	}
}
```