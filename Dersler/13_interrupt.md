while dongüsünde yanıp sönen led interrupt ile değiştiriliyor.

```c
#include "stm32f4xx.h"                  // Device header
int pin=15;

void EXTI0_IRQHandler(){
	EXTI->PR |= EXTI_PR_PR0;
	if(pin==15)
		pin=14;
	else
		pin=15;
}

int main(){
	int i;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0;
	
	SYSCFG->EXTICR[0] &= ~SYSCFG_EXTICR1_EXTI0;
	
	EXTI->IMR |= EXTI_IMR_MR0;
	
	EXTI->RTSR |= EXTI_RTSR_TR0;
	
	NVIC_EnableIRQ(EXTI0_IRQn);
		
	while(1){
		if(pin==15){
			GPIOD->ODR |= GPIO_ODR_OD15;
			for(i=0;i<1000000;i++);
			GPIOD->ODR &= ~GPIO_ODR_OD15;
			for(i=0;i<1000000;i++);
		}else{
			GPIOD->ODR |= GPIO_ODR_OD14;
			for(i=0;i<1000000;i++);
			GPIOD->ODR &= ~GPIO_ODR_OD14;
			for(i=0;i<1000000;i++);
		}	
	}
}
```