```c
#include "stm32f4xx.h" 

unsigned int adc;
float voltaj;    

int main ()  { 
    
    int i;
    
    //A0 Pini kullanıldığı için A portuna clk sinyali veriliyor.
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
    
    //A portunun 0. pininin modu moder yazmacının ilgili kısmına 11 yazılarak analog olarak ayarlandi.
    GPIOA->MODER |= GPIO_MODER_MODER0_0 | GPIO_MODER_MODER0_1; 

    //ADC1 APB2 busına bagli oldugu icin APB2'nin clocku etkinlestirildi.
    RCC->APB2ENR |= RCC_APB2ENR_ADC1EN; 
    
	//ADC'ye enerji veriliyor. 
    ADC1->CR2 |= ADC_CR2_ADON;
	
    //Devamli Mod: bir çevrim işlemi bittiğinde otomatik olarak diğer işlem başlayacak
    ADC1->CR2 |=ADC_CR2_CONT;
    
    //Analog-Dijital Çevrime Basla
    ADC1->CR2 |= ADC_CR2_SWSTART;

    while(1){
        for(i=0;i<10000;i++);
        adc=ADC1->DR;
        voltaj= ((float)adc/4095)*2.95;   
    }
}
```



```c
#include "stm32f4xx.h" 

unsigned int adc;
float voltaj1, voltaj2;
int i=0;

int main ()  { 
      
    //A0 Pini kullanıldığı için A portuna clk sinyali veriliyor.
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
    
    //A portunun 0. pininin modu moder yazmacının ilgili kısmına 11 yazılarak analog olarak ayarlandi.
    GPIOA->MODER |= GPIO_MODER_MODE0_0 | GPIO_MODER_MODE0_1; 
		GPIOA->MODER |= GPIO_MODER_MODE1_0 | GPIO_MODER_MODE1_1; 

    //ADC1 APB2 busına bagli oldugu icin APB2'nin clocku etkinlestirildi.
    RCC->APB2ENR |= RCC_APB2ENR_ADC1EN; 
    
	//ADC'ye enerji veriliyor. 
    ADC1->CR2 |= ADC_CR2_ADON;
    
	//tarama modu iki tane arka arkaya ADC yapilacak
	ADC1->CR1 |= ADC_CR1_SCAN;
	
	//Devamli Mod: bir çevrim işlemi bittiğinde otomatik olarak diğer işlem başlayacak
    ADC1->CR2 |=ADC_CR2_CONT;
			
	// tarama modu her  kanalin çevirilmesi sonrasi EOC biti set edilecek
	//Overrun olabilir dikkat!!!
	ADC1->CR2 |=ADC_CR2_EOCS;
		
	//her çevrime 480 cycle ekledik
	ADC1->SMPR2 |= ADC_SMPR2_SMP0_0 | ADC_SMPR2_SMP0_1 | ADC_SMPR2_SMP0_2;	
	ADC1->SMPR2 |= ADC_SMPR2_SMP1_0 | ADC_SMPR2_SMP1_1 | ADC_SMPR2_SMP1_2;
		
	//sequence 2 pinden çevrim
	ADC1->SQR1 |= ADC_SQR1_L_0;
		
	//birinci sirada 00000 oldugundan degistirilmedi.
	//ikinci siraya 1 yazilarak 1. kanaldan okunacagi belirtilmis oldu.
	ADC1->SQR3 |= ADC_SQR3_SQ2_0;
		
	//Bus clocku 8'e bölündü.	
	ADC123_COMMON->CCR |= ADC_CCR_ADCPRE_0 | ADC_CCR_ADCPRE_1; 

    //Analog-Dijital Çevrime Basla
    ADC1->CR2 |= ADC_CR2_SWSTART;
		
    while(1){	
		while(!(ADC1->SR & ADC_SR_EOC));
			
		if(i%2==0){
			adc=ADC1->DR;
			voltaj1= ((float)adc/4096.)*3;
		}else{
			adc=ADC1->DR;
			voltaj2= ((float)adc/4096.)*3;
		}
		i++;
    }
}
```