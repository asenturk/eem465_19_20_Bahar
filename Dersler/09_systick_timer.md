```c
#include "stm32f4xx.h" 

int main(){
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER12_0;
	
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->LOAD = 16000000;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD12;
		while(!(SysTick->CTRL &  SysTick_CTRL_COUNTFLAG_Msk)); 
		
		GPIOD->ODR &= ~GPIO_ODR_OD12;
		while(!(SysTick->CTRL & SysTick_CTRL_COUNTFLAG_Msk)); 
	}
}
```


```c
#include "stm32f4xx.h" 

void systick_init(){
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->LOAD = 16000000;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}

void bekle(){
	while(!(SysTick->CTRL &  SysTick_CTRL_COUNTFLAG_Msk)   ); 
}

int main(){
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER12_0;
	
	systick_init();
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD12;	
		bekle();
		
		GPIOD->ODR &= ~GPIO_ODR_OD12;
		bekle();
	}
}
```



```c
#include "stm32f4xx.h" 

void systick_init(){
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->LOAD = 16000000;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}

void bekle(){
	while(!(SysTick->CTRL &  SysTick_CTRL_COUNTFLAG_Msk)   ); 
}

int main(){
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	
	systick_init();
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD12;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD12;
		SysTick->LOAD = 8000000;
		bekle();
		
		GPIOD->ODR |= GPIO_ODR_OD13;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD13;
		SysTick->LOAD = 16000000;
		bekle();	
	}
}
```




```c
#include "stm32f4xx.h" 

void systick_init(){
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->LOAD = 16000000;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}

void bekle(){
	while(!(SysTick->CTRL &  SysTick_CTRL_COUNTFLAG_Msk)   ); 
}

int main(){
	int i;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER13_0;
	
	systick_init();
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD13;
		for(i=0;i<4;i++)
			bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD13;
		bekle();
	}
}
```


```c
#include "stm32f4xx.h"

void systick_init(){
	SysTick->LOAD = 16000000*0.5;
	SysTick->VAL = 0;
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk;
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}
void bekle(){
	while(!(SysTick->CTRL &   SysTick_CTRL_COUNTFLAG_Msk  ));
}
int main(){
	int i=0;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	systick_init();
	while(1){
		GPIOD->ODR &=  ~(0x3U << 12); 
		GPIOD->ODR |= (i%4) << 12;
		bekle();
		i++;	
	}
}
```