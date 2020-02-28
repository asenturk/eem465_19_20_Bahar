```c
typedef  unsigned char BOOL;
int main(){
	BOOL a;
	a=1;
}
```

```c
#include <string.h>
struct calisan{
	char isim[50];
	int sicil;
	float maas;
};
int main(){
	struct calisan calisan1;
	strcpy(calisan1.isim, "calisan ismi");
	calisan1.sicil=12345;
	calisan1.maas=5247.8;
}
```

```c
#include <string.h>
struct calisan{
	char isim[50];
	int sicil;
	float maas;
};
int main(){	
	struct calisan calisan1, calisan2;
	struct calisan *p;
	
	strcpy(calisan1.isim, "calisan ismi");
	calisan1.sicil=12345;
	calisan1.maas=5247.8;
	
	p = &calisan2;
	
	strcpy((*p).isim, "calisan2 ismi");
	(*p).sicil = 2345;
	(*p).maas = 4512.9;
	// yukarıdaki kullanım yerine
	// -> operatörü de kullanılabilir.
	// p->maas = 4512.9;
}
```

```c
#include<string.h>

typedef struct {
	char isim[50];
	int sicil;
	float maas;
} calisan;

int main(){	
	calisan calisan2;
	calisan *p;
	p = &calisan2;
	strcpy((*p).isim, "calisan2 ismi");
	(*p).maas=4572.9;
	(*p).sicil =2345;
	
	//(*p).maas=5000.9;
	p->maas=5000.9;
	

}
```


```c
#define RCC_AHB1ENR (*((unsigned int*)0x40023830))
typedef struct{
	unsigned int MODER;
	unsigned int X[4];
	unsigned int ODR;
} GPIO_TypeDef;

int main(){
	int i;
	GPIO_TypeDef *GPIOD;
	GPIOD = (GPIO_TypeDef*)0x40020C00;

	RCC_AHB1ENR |= 1<<3;
	GPIOD->MODER |= 1<<30;
	while(1){
		
		GPIOD->ODR |= 1<<15;
		for(i=0;i<1000000;i++);
		GPIOD->ODR &= ~(1<<15);
		for(i=0;i<1000000;i++);	
	}
}
```


```c
#define RCC_AHB1ENR (*((unsigned int*)0x40023830))
#define GPIOD ((GPIO_TypeDef*)0x40020C00)

typedef struct{
	unsigned int MODER;
	unsigned int X[4];
	unsigned int ODR;
} GPIO_TypeDef;

int main(){
	int i;
	
	RCC_AHB1ENR |= 1<<3;
	GPIOD->MODER |= 1<<30;
	while(1){
		
		GPIOD->ODR |= 1<<15;
		for(i=0;i<1000000;i++);
		GPIOD->ODR &= ~(1<<15);
		for(i=0;i<1000000;i++);
	
	}
}
```

```c
#define RCC_AHB1ENR (*((unsigned int *)0x40023830))
#define GPIOD 	((GPIO_TypeDef*)0x40020C00)
#define RCC_AHB1ENR_GPIODEN (1<<3)
#define GPIO_MODER_MODER15_0 (1<<30)
#define GPIO_ODR_OD15 (1<<15)

typedef struct{
	unsigned int MODER;
	unsigned int X[4];
	unsigned int ODR;
} GPIO_TypeDef;

int main(){
	int i;
	RCC_AHB1ENR |= RCC_AHB1ENR_GPIODEN;	
	GPIOD->MODER |= GPIO_MODER_MODER15_0;
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD15;
		for(i=0;i<1000000;i++);
		GPIOD->ODR &=   ~GPIO_ODR_OD15;
		for(i=0;i<1000000;i++);
	}
}
```


```c
#include "stm32f4xx.h"
int main(){
	int i;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;
	GPIOD->MODER |= GPIO_MODER_MODER15_0;
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD15;
		for(i=0;i<1000000;i++);
		GPIOD->ODR &= ~GPIO_ODR_OD15;
		for(i=0;i<1000000;i++);
	}
}
```


```c
#include "stm32f4xx.h" 
int main(){
	int i;
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN;	
	GPIOD->MODER |= GPIO_MODER_MODER15_0 |
			GPIO_MODER_MODER12_0;
	
	while(1){
		GPIOD->ODR |= GPIO_ODR_OD15 |
			GPIO_ODR_OD12;
		for(i=0;i<1000000;i++);
		GPIOD->ODR &= ~(GPIO_ODR_OD15 | GPIO_ODR_OD12);
		for(i=0;i<1000000;i++);
	}
}
```