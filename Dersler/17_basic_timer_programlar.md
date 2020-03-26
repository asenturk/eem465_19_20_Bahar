Basic timer kullanımı, Busy wait yöntemi ile (beklemek için bayrak kontrol ederek) LED yakma söndürme.

```c
#include "stm32f4xx.h"

int main(){

	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER14_0;
	GPIOD->ODR |= GPIO_ODR_OD14;
	
	RCC->APB1ENR |= RCC_APB1ENR_TIM6EN;
	
	TIM6->PSC &= ~TIM_PSC_PSC;
	TIM6->PSC |= (999U & TIM_PSC_PSC);
	
	TIM6->ARR &= ~TIM_ARR_ARR;
	TIM6->ARR |= (6400 & TIM_ARR_ARR);
	
	TIM6->CR1 |= TIM_CR1_CEN;
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD14;
		while(!(TIM6->SR & TIM_SR_UIF));
		TIM6->SR &= ~TIM_SR_UIF;
		
		GPIOD->ODR &= ~GPIO_ODR_OD14;
		while(!(TIM6->SR & TIM_SR_UIF));
		TIM6->SR &= ~TIM_SR_UIF;
		
	}
}
```


Basic timer kesmesi ile LED yakma söndürme

```c
#include "stm32f4xx.h"

void TIM6_DAC_IRQHandler(){
	TIM6->SR &= ~TIM_SR_UIF;
	if(GPIOD->ODR & GPIO_ODR_OD12)
		GPIOD->ODR &= ~GPIO_ODR_OD12;
	else
		GPIOD->ODR |= GPIO_ODR_OD12;
}
int main(){
	
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER12_0;
	
	RCC->APB1ENR |= RCC_APB1ENR_TIM6EN;
	
	TIM6->PSC &= ~TIM_PSC_PSC;
	TIM6->PSC |= (1599U & TIM_PSC_PSC);
	
	TIM6->ARR &= ~TIM_PSC_PSC;
	TIM6->ARR |= 3000U & TIM_PSC_PSC;
	
	TIM6->DIER |= TIM_DIER_UIE;
	NVIC_EnableIRQ(TIM6_DAC_IRQn);
	
	TIM6->CR1 |= TIM_CR1_CEN;

	while(1);		
}
```


