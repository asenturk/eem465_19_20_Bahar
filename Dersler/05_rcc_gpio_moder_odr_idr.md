```c
#include "stm32f4xx.h"

void bekle(void);

int main(){
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 |
			GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;

	for(;;){	
		GPIOD->ODR |= GPIO_ODR_OD15;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD15;
		bekle();
		
		GPIOD->ODR |= GPIO_ODR_OD14;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD14;
		bekle();
		
		GPIOD->ODR |= GPIO_ODR_OD13;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD13;
		bekle();
		
		GPIOD->ODR |= GPIO_ODR_OD12;
		bekle();
		GPIOD->ODR &= ~GPIO_ODR_OD12;
		bekle();
	}	
}

void bekle(void){
	int i;
	for(i=0;i<1000000;i++);
}
```


```c
#include "stm32f4xx.h"

void bekle(void);

int main(){
	unsigned  led;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 |
			GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;

	for(;;){	
		for(led=15; led>=12; led--){
			GPIOD->ODR |= 0x1UL << led;
			bekle();
			GPIOD->ODR &= ~(0x1UL << led);
			//bekle();
		}	
	}
}
void bekle(void){
	int i;
	for(i=0;i<3000000;i++);
}
```


```c
#include "stm32f4xx.h"

void bekle(void);

int main(){
	unsigned  led;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 |
			GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	for(;;){
		for(led=15; led>=12; led--){
			GPIOD->ODR |= 0x1UL << led;
			bekle();
			GPIOD->ODR &= ~(0x1UL << led);	
		}
		for(led=13; led<=14; led++){
			GPIOD->ODR |= 0x1UL << led;
			bekle();
			GPIOD->ODR &= ~(0x1UL << led);	
		}
	}
}
void bekle(void){
	int i;
	for(i=0;i<3000000;i++);
}
```


```c
#include "stm32f4xx.h"

void bekle(void);

int main(){
	unsigned  led;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 |
			GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	for(;;){
		for(led=12; led<=15; led++){
			GPIOD->ODR |= 0x1UL << led;
			bekle();
		}
		for(led=12; led<=15; led++){
			GPIOD->ODR &= ~(0x1UL << led);
			bekle();
		}
	}
}
void bekle(void){
	int i;
	for(i=0;i<3000000;i++);
}
```


```c
#include "stm32f4xx.h"

void bekle(int x);

int main(){
	unsigned  led;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 |
			GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	for(;;){
		for(led=12; led<=15; led++){
			GPIOD->ODR |= 0x1UL << led;
			bekle(led-11);
		}
		for(led=15; led>=12; led--){
			GPIOD->ODR &= ~(0x1UL << led);
			bekle(led-11);
		}
	}
}
void bekle(int x){
	int i;
	for(i=0;i<1000000*x;i++);
}
```



```c
#include "stm32f4xx.h"

void bekle(int x);

int main(){
	unsigned  led;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOAEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 |
			GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	
	//butonun pinini input modunda ayarlamak için moder yazmacında
	//ilgili bölgeye 00 yazmak gerekli.
	//reset durumunda yazmacta ilgili kısım 00 olduğundan pinin modu zaten input 
	//ama nasil yapildigini gormek icin bu kod yazdik
	GPIOA->MODER &= ~(GPIO_MODER_MODER0_1 | GPIO_MODER_MODER0_0);
	
	for(;;){
		if(GPIOA->IDR & 0x1UL << 0U){	
			for(led=12; led<=15; led++){
				GPIOD->ODR |= 0x1UL << led;
				bekle(led-11);
			}
			for(led=15; led>=12; led--){
				GPIOD->ODR &= ~(0x1UL << led);
				bekle(led-11);
			}
		}
	}
}
void bekle(int x){
	int i;
	for(i=0;i<1000000*x;i++);
}
```





