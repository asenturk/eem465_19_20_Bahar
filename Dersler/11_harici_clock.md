```c
#include "stm32f4xx.h"

void systick_init(){
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->LOAD = 8000000;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}

void bekle(){
	while(!(SysTick->CTRL &  SysTick_CTRL_COUNTFLAG_Msk)   ); 
}

int main(){
	
	RCC->CR |= RCC_CR_HSEON;
	while(!(RCC->CR & RCC_CR_HSERDY));
	RCC->CFGR |= RCC_CFGR_SW_0;
	RCC->CR &= ~RCC_CR_HSION;
	
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER13_0;
	systick_init();
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD13;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD13;
		bekle();
	}
}
```


```c
#include "stm32f4xx.h"

void systick_init(){
	//SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->LOAD = (8000000 / 8) * 0.1;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}

void bekle(){
	while(!(SysTick->CTRL &  SysTick_CTRL_COUNTFLAG_Msk)   ); 
}

int main(){
	
	RCC->CR |= RCC_CR_HSEON;
	while(!(RCC->CR & RCC_CR_HSERDY));
	RCC->CFGR |= RCC_CFGR_SW_0;
	RCC->CR &= ~RCC_CR_HSION;
	
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER13_0;
	systick_init();
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD13;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD13;
		bekle();
	}
}
```