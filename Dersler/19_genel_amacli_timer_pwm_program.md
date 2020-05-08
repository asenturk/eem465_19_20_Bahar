```c
#include "stm32f4xx.h"
int main(){
// 1. TIM3 APB1 busina bagli
// 2. Alternatif fonksiyon 2 yi kullanacagiz
// 3. Kanal3 PC8, Kanal2 PB5'i kullanalim.

	RCC->AHB1ENR |=	RCC_AHB1ENR_GPIOBEN | RCC_AHB1ENR_GPIOCEN;
	GPIOB->MODER |= GPIO_MODER_MODER5_1;
	GPIOC->MODER |= GPIO_MODER_MODER8_1;
	
	GPIOB->AFR[0] |= GPIO_AFRL_AFSEL5_1;
	GPIOC->AFR[1] |= GPIO_AFRH_AFSEL8_1;
	
	RCC->APB1ENR |= RCC_APB1ENR_TIM3EN;
	
	// Asagi sayan sayici
	TIM3->CR1 |=TIM_CR1_DIR;
	
	// PSC 15 olursa zamanlayicinin frekansi 1 MHz olur.
	 TIM3->PSC = 15;
	
	// PWM sinyalinin frekansi 100 hz olsun.
	// 1 periyot 1/100 = 10^-2 saniye olacak.
	// zamanlayici 0.01 x 10^6=10000 sayi saymali
	TIM3->ARR =10000-1;
	
	// %20 görev çevrimi için 
	TIM3->CCR2 = 2000-1;
	
	// %60 görev çevrimi için 
	TIM3->CCR3 = 6000-1;
	
	// CNT<CCR oldugunda output aktif
	TIM3->CCMR1 |= TIM_CCMR1_OC2M_2 | TIM_CCMR1_OC2M_1 | TIM_CCMR1_OC2M_0;
	TIM3->CCMR2 |= TIM_CCMR2_OC3M_2 | TIM_CCMR2_OC3M_1 | TIM_CCMR2_OC3M_0;
	
	TIM3->CCER |= TIM_CCER_CC2E | TIM_CCER_CC3E;
	TIM3->CR1 |= TIM_CR1_CEN;
	while(1);
}
```

```c
#include "stm32f4xx.h"
void TIM4_IRQHandler(void){
	TIM4->SR &= ~TIM_SR_UIF;
	TIM4->CCR4 = TIM4->CCR4+50;
	if(TIM4->CCR4 >= 20000-50)
		TIM4->CCR4 = 0;
}
int main(){	
	// TIM4, APB1 busina bagli
	// Alternatif fonksiyon 2 yi kullanacagiz
	// Kanal4 PD15 

	RCC->AHB1ENR |=	RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_1;
	GPIOD->AFR[1] |= GPIO_AFRH_AFSEL15_1;
	
	RCC->APB1ENR |= RCC_APB1ENR_TIM4EN;
	
	TIM4->CR1 |=TIM_CR1_DIR;
	
	//PSC 1 olursa zamanlayicinin frekansi 8 MHz olur.
	 TIM4->PSC = 1;
	
	//PWM sinyalinin frekansi 400 hz olsun.
	// 1 periyot 1/400 = 2.5 x 10^-3 saniye olacak.
	// zamanlayici (2.5 x 10^-3) x (8x10^6)=20000 sayi saymali
	TIM4->ARR =20000-1;
	
	TIM4->CCR4 = 50;
	
	TIM4->CCMR2 |= TIM_CCMR2_OC4M_2 | TIM_CCMR2_OC4M_1;
	TIM4->CCER |= TIM_CCER_CC4E;
	
	TIM4->DIER |= TIM_DIER_UIE;
	NVIC_EnableIRQ(TIM4_IRQn);
	
	TIM4->CR1 |= TIM_CR1_CEN;
	while(1);
}
```