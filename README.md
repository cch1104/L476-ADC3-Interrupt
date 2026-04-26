# L476-ADC3-Interrupt

A simple embedded system project using **STM32 NUCLEO-L476RG** to measure analog voltage through **ADC interrupt mode** and display the result on a **16x2 LCD1602**.

---

## Project Overview

This project reads analog voltage from a potentiometer or external source through **ADC1 Channel 3**, converts it into millivolts, and displays the value on LCD.

### Features

- STM32 ADC Configuration
- Interrupt Driven Conversion
- LCD1602 4-bit Interface
- GPIO Control
- Embedded C Development
- Real-time Voltage Monitoring

---

## Hardware Used

- STM32 NUCLEO-L476RG
- LCD1602
- Potentiometer (10K)
- Breadboard
- Jumper Wires
- USB Power

---

## MCU Clock Configuration

### Clock Source

- Internal HSI Oscillator = 16 MHz
- PLL Enabled
- System Clock = 40 MHz

### PLL Formula

```text
PLL Input  = 16MHz / 2 = 8MHz
PLL Output = 8MHz × 10 = 80MHz
SYSCLK     = 80MHz / 2 = 40MHz
````

---

## System Block Diagram

```text
┌──────────────┐
│ Potentiometer│
└──────┬───────┘
       │ Analog Voltage
       ▼
┌──────────────┐
│ ADC1 CH3     │
│ STM32L476RG  │
└──────┬───────┘
       │ 12-bit ADC Data
       ▼
┌──────────────┐
│ Voltage Calc │
│ adc*3300/4095│
└──────┬───────┘
       ▼
┌──────────────┐
│ LCD1602      │
│ Display mV   │
└──────────────┘
```

---

## GPIO Configuration

### LCD Pins

| LCD Signal | STM32 Pin |
| ---------- | --------- |
| RS         | PA6       |
| EN         | PA7       |
| D4         | PA8       |
| D5         | PA9       |
| D6         | PA10      |
| D7         | PA11      |

### ADC Input

| Function | STM32 Pin |
| -------- | --------- |
| ADC1_IN3 | PA2       |

---

## ADC Configuration

| Parameter       | Value          |
| --------------- | -------------- |
| Resolution      | 12-bit         |
| Data Align      | Right          |
| Scan Mode       | Disable        |
| Continuous Mode | Disable        |
| Trigger         | Software Start |
| Interrupt       | Enabled        |
| Channel         | ADC_CHANNEL_3  |
| Sampling Time   | 12.5 Cycles    |

---

## Software Flowchart

```text
Power ON
   ↓
HAL_Init()
   ↓
SystemClock_Config()
   ↓
GPIO Init
ADC1 Init
LCD Init
   ↓
Display "Voltmeter"
   ↓
Start ADC Interrupt
   ↓
while(1)
   ↓
Conversion Done?
   ↓ Yes
Read ADC Value
   ↓
Restart ADC
   ↓
Convert to mV
   ↓
Show on LCD
   ↓
Repeat
```

---

## Main Logic

```c
HAL_ADC_Start_IT(&hadc1);

while(1)
{
    if(convend == 1)
    {
        convend = 0;

        adcResult = HAL_ADC_GetValue(&hadc1);

        HAL_ADC_Start_IT(&hadc1);

        mv = ((float)adcResult) * 3300.0 / 4095.0;

        sprintf(buff, "%7.2f mV", mv);

        lcd_Goto(0,1);
        lcd_Puts(buff);
    }
}
```

---

## Interrupt Callback

```c
void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc)
{
    convend = 1;
}
```

---

## Example LCD Output

```text
Voltmeter
1234.56 mV
```

---

## Skills Demonstrated

* STM32CubeIDE
* STM32 HAL Driver
* ADC Peripheral
* Interrupt Programming
* GPIO Control
* LCD Driver
* Embedded Debugging

---

## Future Improvements

* DMA Continuous Sampling
* Moving Average Filter
* OLED Display Upgrade
* UART Logging
* Battery Voltage Meter
* Multi-channel ADC

---

