---
title: "Keyboard Pcb Design"
date: 2021-04-11T18:37:25-07:00
draft: false
---

# Feature highlights

Initially, I wanted to build a split board. However, since I already have a
split (crkbd), making a Arteus/Reviuge-like board makes more sense. 

The design is using aggressive stagger (same column stagger like my split
design), and 15 degree angle of columns. 

Other features:

* OLED status display
* RGB underglow
* Audio
* Encoder

# Design considerations for STM32

I'll be using F411 due to lack of F072 stock. 

Tips for setup a stm chip in qmk: https://discord.com/channels/440868230475677696/440870965728116754/839978277489082370

Snippet to find keyboards that uses a certain chip:

```
 % git grep 'MCU\s*=\s*STM32F411' keyboards/
 keyboards/handwired/onekey/blackpill_f411/rules.mk:MCU = STM32F411
 keyboards/handwired/onekey/blackpill_f411_tinyuf2/rules.mk:MCU = STM32F411
 keyboards/handwired/pill60/blackpill_f411/rules.mk:MCU = STM32F411
 keyboards/handwired/riblee_f411/rules.mk:MCU = STM32F411
 keyboards/matrix/m20add/rules.mk:MCU = STM32F411
 keyboards/matrix/noah/rules.mk:MCU = STM32F411
 keyboards/tkw/grandiceps/rules.mk:MCU = STM32F411
 keyboards/tkw/stoutgat/v2/f411/rules.mk:MCU = STM32F411
 keyboards/zvecr/zv48/f411/rules.mk:MCU = STM32F411
```

## DMA channels for peripherals


Peripherals like I2C, SPI and ADC, DAC uses DMA channels. PWM also can use DMA
for better performance, for example, for LED dreivers. STM32F072 has
7 DMA channels(details can be found in reference doc). When choosing 
peripherals, we need to choose carefully so that DMA channels won't collide.


QMK uses DMA for the following:
- WS2812 LED: pwm driver uses DMA+PWM.
- Audio: DAC driver uses DMA. PWM driver doesn't use DMA.
- OLED: Uses I2C which uses DMA channels.

### F072 DMA details

F072 only has one DMA controller(`DMA1`), which has 1 channel, with 7 streams.

- I2C1: uses DMA streams 6/7(or 2/3). (Requires [magic boot code](#oled) 
to configure.)
- DAC1/2: uses DMA streams 3 and 4.
- SPI1: streams 2/3.
- SPI2: streams 4/5 or 6/7.
- TIM3_CH1: stream 4
- TIM1_CH1: stream 2

### F411 DMA details

F411 has 2 DMA controllers, each has 8 channels and each channel with 8 streams.

Summary of DMA usage in my design:

| Feature  |  QMK Driver | Pins  |  Peripheral |  DMA channels|
|----------|-------------|-------|-------------|--------------|
| OLED     |   I2C       |PB6/7  |  I2C1       |  DMA1-Chan1-Stream5/6 |
| Audio    |  PWM        | PA8   | TIM1_CH1 + TIM6 GPT| N/A   |
| RGB LED  |  PWM        | PB1   | TIM3_CH4    |  DMA1-Chan5-Stream2 |
| RGB (alt)|  SPI1       | PB5   | SPI1        |  DMA2-Chan3-Stream2/3        |

## Audio Driver

Two options: DAC and PWM. DAC would occupy one or two DMA channels. PWM doesn't
use DMA. 

### Audio with DAC

Using two pins can provides higher voltage amplitude and louder volume. 

* Pins(A4/A5). [ref](https://docs.qmk.fm/#/audio_driver?id=arm),
[ref2](https://docs.qmk.fm/#/feature_audio?id=arm-based-boards)
```
// Audio configuration
#define AUDIO_PIN A4
#define AUDIO_PIN_ALT A5
#define AUDIO_PIN_ALT_AS_NEGATIVE
#define A4_AUDIO
#ifndef STARTUP_SONG
#    define STARTUP_SONG SONG(STARTUP_SOUND)
#endif  // STARTUP_SONG
```

### Audio with PWM

* Use any timer's PWM to output square wave. This only support single pin mode.

We use `TIM3_CH1` as example. (use tim6 for audio state timer)

```
//halconf.h:
#define HAL_USE_PWM                 TRUE
#define HAL_USE_PAL                 TRUE
#define HAL_USE_GPT                 TRUE
#include_next <halconf.h>

// mcuconf.h:
#include_next <mcuconf.h>
#undef STM32_PWM_USE_TIM1
#define STM32_PWM_USE_TIM1                  TRUE
#undef STM32_GPT_USE_TIM6
#define STM32_GPT_USE_TIM6                  TRUE

//config.h:
// Use pin C6(TIM3_CH1) PWM
#define AUDIO_PIN A8
#define AUDIO_PWM_PAL_MODE 1
#define AUDIO_PWM_DRIVER PWMD1
#define AUDIO_PWM_CHANNEL 1
#define AUDIO_STATE_TIMER GPTD6
```

## OLED

I2C: Use I2C1(pins B6/7). [ref](https://docs.qmk.fm/#/i2c_driver?id=arm-configuration)

### Magic fix

For F072, extra configuration is needed to use I2C1 correctly. For details, see
[discord](https://discord.com/channels/440868230475677696/440868230475677698/837318487759388682),
[discord2](https://discord.com/channels/440868230475677696/440868230475677698/837292700051046400)

### Example code

```
// example config: https://github.com/qmk/qmk_firmware/blob/master/keyboards/xelus/kangaroo/config.h
//set I2C1_SCL_PAL_MODE and I2C1_SDA_PAL_MODE to 1. (PINs B6/7)

#define I2C1_TIMINGR_SCLDEL 3U
#define I2C1_TIMINGR_SDADEL 1U
#define I2C1_TIMINGR_SCLH     3U
#define I2C1_TIMINGR_SCLL   9U

//Copy board_init() from https://github.com/qmk/qmk_firmware/blob/master/keyboards/xelus/kangaroo/kangaroo.c :

void board_init(void) {
  SYSCFG->CFGR1 |= SYSCFG_CFGR1_I2C1_DMA_RMP;
  SYSCFG->CFGR1 &= ~(SYSCFG_CFGR1_SPI2_DMA_RMP);
}

//The problem is that platforms/chibios/GENERIC_STM32_F072XB/configs/mcuconf.h contains:
#define STM32_I2C_I2C1_RX_DMA_STREAM STM32_DMA_STREAM_ID(1, 7)
#define STM32_I2C_I2C1_TX_DMA_STREAM STM32_DMA_STREAM_ID(1, 6)

//But this is actually an alternate configuration for I2C1 DMA streams, and it needs to be enabled by setting the SYSCFG_CFGR1_I2C1_DMA_RMP bit.

//The SYSCFG_CFGR1_SPI2_DMA_RMP clearing part might not actually be needed (the power-on state for this bit should be 0), but you can include it just to be sure.

```

## Backlight RGB: 

* LED: use [WS2812S](https://lcsc.com/product-detail/Light-Emitting-Diodes-LED_5050-RGBIntegrated-Light_C114584.html)

### RGB driver using PWM
Uses PWM and DMA. Here I'm using `TIM3_CH4` on pin `B1` as output, and connect
it with pull up resistor to 5V. (`B1` is a 5v tolerant pin)

```
// rules.mk
RGBLIGHT_DRIVER = WS2812
// config.h 
#define RGB_DI_PIN B1
#define WS2812_PWM_DRIVER PWMD3
#define WS2812_PWM_CHANNEL 4
#define WS2812_EXTERNAL_PULLUP
// Set B1 to tim3 ch4
#define WS2812_PWM_PAL_MODE 2
// DMA request mapped on this DMA channel only if the corresponding remapping 
// bit is cleared in the SYSCFG_CFGR1 register. For more details, please refer 
// to Section 9.1.1: SYSCFG configuration register 1 (SYSCFG_CFGR1) on page165.
#define WS2812_DMA_STREAM STM32_DMA1_STREAM2
#define WS2812_DMA_CHANNEL 5
```

If using pin B15(tim15) (F072)

```
// rules.mk
RGBLIGHT_DRIVER = WS2812
// config.h 
#define RGB_DI_PIN B15
#define WS2812_EXTERNAL_PULLUP
// using TIM15 channel 2
#define WS2812_PWM_DRIVER PWMD15
#define WS2812_PWM_CHANNEL 2
// Set B15 to tim15 ch2
#define WS2812_PWM_PAL_MODE 1
// DMA setup needs to be verified.
#define WS2812_DMA_STREAM STM32_DMA1_STREAM5
//mcuconf.h if using tim15
//#define STM32_TIM15_SUPPRESS_ISR
```

### RGB driver SPI
Use `SPI2`(B15). [ref](https://docs.qmk.fm/#/ws2812_driver?id=spi)
 [spi](https://docs.qmk.fm/#/spi_driver?id=chibiosarm-configuration).
  This requires other spi pins unused.(miso and sck, e.g. B13 and B14)

```
#define WS2812_SPI SPID2
// Pins 15/14/13
#define WS2812_SPI_MOSI_PAL_MODE 0
#define WS2812_SPI_MOSO_PAL_MODE 0
#define WS2812_SPI_SCK_PAL_MODE 0
```

Note: Extra setup is needed for F072 `B15` pin. [details](https://discord.com/channels/440868230475677696/440868230475677698/838685140661436417)

The fix is to define `#define WS2812_SPI_USE_CIRCULAR_BUFFER`. The flag is only
available in `develop` branch. Anoth 


# Component choices

Use this [site](https://yaqwsx.github.io/jlcparts/#/) to search JLCPCB parts
for SMD assembly service availability and price.

Summary:

| Component  |  Model   | JLCPCB PN |Count|Notes | 
|------------|----------|-----------|-----|------|
| MCU        |STM32F072CBU6| C92504 |1   |
| Regulator  |XC6206P332MR| C5446   | 1  | 3.3V fixed output|
| BJT        |MMBT3904    | C20526  | 1  | for reset circuit, need add resistors|
| diode      | 1N4148     |C81598  | for both switches and reset button.|
| Fuse       |JK-MSMD050    | C369167 | For USB bus voltage protection|
| Schottky Diode| SS14    | C2480  | between voltage regulartor|
| Buzzer    | KLJ-1625    | C201041| smd pizeo buzzer |


## MCU

I'm using STM32 MCU, which is more powerful than the atmega on pro micros.
The atmega32u4 is 8 bit processor and only has 32K memory size. In comparison,
the Arm chips are 32bit, and has more program memory(F072C8 has 64K, F072CB has
128K, F411 has 256K/521K flash and 128K SRAM).

Initially I wanted to use STM32F072CBU6, which is also used in Ferris. However,
it became OOS and I had to pick another chip. I found STM32F411 whixh id more 
powerful.

One caveat is that F4 series doesn't have built-in eeprom emulator. An
external eeprom chip is needed, otherwise some features are not usable, like 
bootmagic, on-board settings(default layers, for example).

## Reset button circuit

To reset(and enter bootloader), we need to trigger both NRST and BOOT0 pins.
This [guide](http://acheronproject.com/reset_article/principle.html#conclusion)
suggested 3 variants and I'm using the middle one for simplicity. For this 
circuit, we need a transistor. I'm not using the prebiased one shown there 
(50V, 100mA, 2.2kOhm and 47kOhm) since it's extended part and has $3 extra 
charge. Instead I'm using a 40V, 200mA NPN transistor and adding external 
resistors.

## Fuse 

Ferris used "Polymeric 15V 1A 100ms 750mΩ 1206 PTC Resettable Fuses RoHS", 
however the model is OOS, so I searched on LCSC and found C369167
which has similar spec. For the diode, Ferris used C435473, and I'm using the
same.

## Schottky Diode

Ferris used RB060MM-30, which is 30V vr, 490mV vf.  I use SS14 which is basic
part in JLCPCB. It is "40V 1A 550mV @ 1A" which is pretty close.

## Resistors and capacitors

I'm using all 0603 footprint.


