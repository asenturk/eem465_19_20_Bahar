```c
int main(){
	int a=3511045, b;
	int n;
	
	//n. biti set etmek
	n=11;
	b= (1 << n) | a; 
	
	//n. biti temizlemek
	n=15;
	b= ~(1 << n) & a;
	
	//n. bitin  tersini elde etmek 
	n=16;
	b= 1 << n ^ a;
	
	//n. biti test etmek
	n=18;
	if( (1 << n) & a){
		b=1;
	}
}
```


```c
int main(){
	
	int a=10, b=20;
	int *p;
	
	p= &a;
	
	*p = *p + 5;
	
	p=&b;
	
	*p = *p + 5;	
}
```

```c
int main(){
	
	float a=10.15;
	float ondalik;
	float tam;
	
	tam = (int)a;
	ondalik=a-tam;
}
```

```c
int main(){

	unsigned int sayi = 0x40023800;
	unsigned int *adres;
	
	adres = (int *)sayi;
	
	*adres = *adres | 1 << 3;
}
```
```c
int main(){

	unsigned int RCC_AHB1ENR_adresi = 0x40023830;
	unsigned int GPIOD_MODER_adresi = 0x40020C00;
	unsigned int GPIOD_ODR_adresi   = 0x40020C14;
	
	int i;
	
	unsigned int *RCC_AHB1ENR;
	unsigned int *GPIOD_MODER;
	unsigned int *GPIOD_ODR;
	
	RCC_AHB1ENR = (int *)RCC_AHB1ENR_adresi;
	*RCC_AHB1ENR = *RCC_AHB1ENR | 1 << 3; //D portuna clock verildi.
	
	GPIOD_MODER = (int *)GPIOD_MODER_adresi;
	*GPIOD_MODER = *GPIOD_MODER | 1 <<30;
	GPIOD_ODR = (int *)GPIOD_ODR_adresi;
	
	while(1){
		*GPIOD_ODR = *GPIOD_ODR | 1 <<15;
		for(i=0;i<1000000;i++);
	
		*GPIOD_ODR = *GPIOD_ODR & ~(1 <<15);
		for(i=0;i<1000000;i++);
	}
}
```


```c
#define RCC_AHB1ENR     (*((int *)0x40023830))
#define GPIOD_MODER     (*((int *)0x40020C00))
#define GPIOD_ODR       (*((int *)0x40020C14))

int main(){
	int i;
	RCC_AHB1ENR |=  1 << 3; //D portuna clock verildi.
	GPIOD_MODER |=  1 <<30; //15. pinin modu output olarak ayarlandi.
	
	while(1){
		GPIOD_ODR |=  1 <<15; //ledi yak
		for(i=0;i<1000000;i++);
		
		GPIOD_ODR &= ~(1 <<15); //ledi söndür
		for(i=0;i<1000000;i++);
	}
}
```


```c
#define RCC_AHB1ENR     (*((int *)0x40023830))
#define GPIOD_MODER     (*((int *)0x40020C00))
#define GPIOD_ODR       (*((int *)0x40020C14))

int main(){
	int i;
	RCC_AHB1ENR |=  1 << 3; //D portuna clock verildi.
	GPIOD_MODER |=  1 <<28 | 1 <<30;  output olarak ayarlandi.
	
	while(1){
		GPIOD_ODR |=  1 <<14 | 1<<15; //ledleri yak
		for(i=0;i<1000000;i++);
		
		GPIOD_ODR &= ~(1 <<14 | 1<<15); //ledleri söndür
		for(i=0;i<1000000;i++);
	}
}
```