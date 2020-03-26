DMA ile LED yakma programı
```c
#include "stm32f4xx.h"

int main(){
int i=0, j;
unsigned short int led_odr[]={0xF000, 0x9000, 0x6000, 0x5000, 0xA000};		
	
	RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN ;
	GPIOD->MODER |= GPIO_MODER_MODER15_0 | GPIO_MODER_MODER14_0 | GPIO_MODER_MODER13_0 | GPIO_MODER_MODER12_0;
	
	RCC->AHB1ENR |= RCC_AHB1ENR_DMA2EN;
	
	// stream 1 kanal 3 ü kullanalım.
	DMA2_Stream1->CR |= DMA_SxCR_CHSEL_0 | DMA_SxCR_CHSEL_1;

	// yön: 01 memory to peripheral veya 10 memory to memory
	DMA2_Stream1->CR |= DMA_SxCR_DIR_1; //mem to mem
		
	//memory address // hedef adres
	DMA2_Stream1->M0AR = (uint32_t) &GPIOD->ODR;
	
	//yazilacak olan verinin uzunlugu çevre birim
	DMA2_Stream1->CR |= DMA_SxCR_PSIZE_0;
	
	//yazilacak olan verinin uzunlugu bellek
	DMA2_Stream1->CR |= DMA_SxCR_MSIZE_0;
	
	//kaç tane transfer yapilacak
	DMA2_Stream1->NDTR |= 1UL;	
	
	while(1){
	//peripheral address //kaynak adres
	DMA2_Stream1->PAR = (uint32_t) &led_odr[i++ % 5];	
		
	//DMAyi baslat 
	DMA2_Stream1->CR |= DMA_SxCR_EN;
				
	for(j=0; j<1000000;j++);
		
		if(DMA2->LISR & DMA_LISR_TCIF1){
			DMA2->LIFCR |= DMA_LIFCR_CTCIF1;
			DMA2->LIFCR |= DMA_LIFCR_CHTIF1;
		}
	}
}
```

DMA ile ADC çevrimlerini belleğin 10 boyutlu dizisine aktarma programı
```c
#include "stm32f4xx.h" 

unsigned int adc;
float voltaj;    

int main ()  {    
    int i;
	unsigned short int data[10];
	
	RCC->AHB1ENR |= RCC_AHB1ENR_DMA2EN;
	
	//DMA request mapping de ADC1 Stream0, Channel0
	//Default degerleri karsilasa da göstermek icin
	DMA2_Stream0->CR &= ~DMA_SxCR_CHSEL;
	
	//veri nereden nereye yazilacak
	//default degerleri kurtariyor ama yine de gösterelim
	//çevre birimden bellege
	DMA2_Stream0->CR &= ~DMA_SxCR_DIR ;
	
	//peripheral address
	DMA2_Stream0->PAR |= (uint32_t) &ADC1->DR;	
	
	//memory address
	DMA2_Stream0->M0AR |= (uint32_t) data; // &data[0]
	
	//yazilacak olan verinin uzunlugu çevre birim
	DMA2_Stream0->CR |= DMA_SxCR_PSIZE_0;
	
	//yazilacak olan verinin uzunlugu bellek
	DMA2_Stream0->CR |= DMA_SxCR_MSIZE_0;
	
	//bellek adresini otomatik arttirma
	DMA2_Stream0->CR |= DMA_SxCR_MINC;
	
	//dairesel mod
	DMA2_Stream0->CR |= DMA_SxCR_CIRC;
	
	//kaç tane transfer yapilacak
	DMA2_Stream0->NDTR |= 10UL;
	
	//veri direct modda gönderilecek. FIFO kullanilmayacak.
	
	 
    //A0 Pini kullanildigi için A portuna clk sinyali veriliyor.
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
    
    //A portunun 0. pininin modu moder yazmacinin ilgili kismina 11 yazilarak analog olarak ayarlandi.
    GPIOA->MODER |= GPIO_MODER_MODER0_0 | GPIO_MODER_MODER0_1; 

    //ADC1 APB2 busina bagli oldugu icin APB2'nin clocku etkinlestirildi.
    RCC->APB2ENR |= RCC_APB2ENR_ADC1EN; 
    
	//ADC'ye enerji veriliyor. 
    ADC1->CR2 |= ADC_CR2_ADON;
		
	//ADC çevrim süresini arttiralim
	//Sample time yazmacin 111 yazilarak 
	//çevirme süresi 12+480  cycle oldu.
	ADC1->SMPR2 |= ADC_SMPR2_SMP0;
	
    //Devamli Mod: bir çevrim islemi bittiginde otomatik olarak diger islem baslayacak
    ADC1->CR2 |=ADC_CR2_CONT;
    
    //Analog-Dijital Çevrime Basla
    ADC1->CR2 |= ADC_CR2_SWSTART;
	
	//ADC tarafindaki DMA yi aktiflestirelim.	
	ADC1->CR2 |=ADC_CR2_DDS;
	ADC1->CR2 |=ADC_CR2_DMA;
	
	//DMAyi baslat
	DMA2_Stream0->CR |= DMA_SxCR_EN;

    while(1){
  
    }
}
```