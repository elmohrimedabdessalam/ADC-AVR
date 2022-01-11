# ADC-AVR
C library of Analog to Digital Converter module for ATmega328 MCU.
this library allow you to use ADC module with interrupt or without, by decommenting ro commenting this line:
```c
#define _ADC_IRQ_EN 
```
## Functions :
```c
void initADC(ANALOG_REF ref);
```
to initiate ADC module, and `ref` variable have a three value possible:
- `AREF` : selecte external voltage reference.
- `AVCC` : the same power supply of MCU.
- `INTERNAL_1_1` : internal 1.1V voltage reference.
```c
uint8_t setAnalogReference(ANALOG_REF ref);
```
to change voltage reference of ADC module.

```c
uint16_t analogRead(ANALOG_CHANNEL adcCh);
```
this function return the value of ACD when complete conversion, to one of the following channel:
`A0`, `A1`, `A2`, `A3`, `A4`, `A5`, `A6` and `A7` (in SMD MCU model), `INT_TEMP_SENSOR`, `BANG_GAP_VOLATGE` and `ANALOG_GND`.
```c
void start_conversion();
```
start conversion, we use this function when using interrupt of ADC.
```c
void ADC_attach_interrupt(void* f_ptr);
```
specifies a function to call when an ADC complete conversion, this function accept as parameter name of custom function.
```c
void ADC_IRQ_disable();
```
to disable occurring interrupt.
## Example:
### 1- Without Interrupt : 
first confim that you comment this line `#define _ADC_IRQ_EN` in `ADC_avr.h` file.
```c 
#include <avr/io.h>
#include <util/delay.h>
#include "ADC/ADC328p.h"
#include "Serial/uart.h"

void print_ADC_value();

int main(void)
{
  int adc;
  
  initADC(AVCC);
  SerialBegin(9600);
  while (1)
  {
    adc = analogRead(A0);
    printf("ADC value: %d\r\n", adc);
    _delay_ms(100);
  }
}
```
### 2- With Interrupt : 
first confim that this line `#define _ADC_IRQ_EN` in `ADC_avr.h` file is uncommented.
```c
#include <avr/io.h>
#include <util/delay.h>
#include "ADC/ADC328p.h"
#include "Serial/uart.h"

void print_ADC_value();

int main(void)
{
  initADC(AVCC);
  attach_interrupt(print_ADC_value);
  SerialBegin(9600);
  sei();
  while (1){
    start_conversion(A0);
    _delay_ms(100);
  }
}
void print_ADC_value()
{
  printf("ADC value is: %d\r\n", ADC);
}
```
