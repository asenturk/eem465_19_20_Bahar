while dongüsünde yanıp sönen led interrupt ile değiştiriliyor.

```c
#include "stm32f4xx.h"
void SysTick_Handler(){
	if(GPIOD->ODR & GPIO_ODR_OD15) // LED yaniyor ise
		GPIOD->ODR &= ~GPIO_ODR_OD15; // LEDi sondur	
	else //LED sonuk ise
		GPIOD->ODR |= GPIO_ODR_OD15; // LEDi yak
}
int main(){
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |=  GPIO_MODER_MODE15_0;
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk; 
	SysTick->VAL = 0UL;
	SysTick->CTRL |= SysTick_CTRL_TICKINT_Msk; 
	SysTick->LOAD |=  16000000-1; 
	// NVIC'te SysTick kesmesinin etkinlestirilmesi
	NVIC_EnableIRQ(SysTick_IRQn);
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
	GPIOD->ODR |= GPIO_ODR_OD15;
	while(1);
}
```


```c
#include "stm32f4xx.h"
int periyot = 160000;
int yuksek = 0;
int dusuk = 160000;
int artis= 1600;
int i=0;
void SysTick_Handler(){
	if (yuksek >= (periyot-artis))
		yuksek = 0;
	if(i%2==0){
		yuksek = yuksek + artis;
		dusuk = periyot - yuksek;
	}
	if(GPIOD->ODR & GPIO_ODR_OD15){ 
		GPIOD->ODR &= ~GPIO_ODR_OD15; 
		SysTick->LOAD =  yuksek; 
	}else{
		GPIOD->ODR |= GPIO_ODR_OD15; 
		SysTick->LOAD =  dusuk; 
	}
	i++;
}
void systick_init(){ 
	NVIC_EnableIRQ(SysTick_IRQn); 
	SysTick->CTRL |= SysTick_CTRL_CLKSOURCE_Msk; 
	SysTick->CTRL |= SysTick_CTRL_TICKINT_Msk; 
	SysTick->LOAD = dusuk; 
	SysTick->CTRL |= SysTick_CTRL_ENABLE_Msk;
}
int main(){
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |=  GPIO_MODER_MODE15_0;
	systick_init();
	while(1);
}	
```