---
title: "Lalboard Stm Design"
date: 2021-05-09T17:23:39-07:00
draft: true
---

# Design considerations

The original lalboard v2 uses esp32 and high customized qmk firmware. My design
goal is to use mcu and code with native qmk code and features if possible.

* MCU: Use STM32 chips. Note that eeprom is available on f3xx and f1xx, as well
as f072, but not on f4xx. External eeprom is needed. like [at24c256](https://lcsc.com/product-detail/EEPROM_MICROCHIP_AT24C256C-SSHL-T_AT24C256C-SSHL-T_C6482.html/?href=jlc-SMT)
* Split comms: single wire half duplex serial. (pull up resistor needed. Can
    directly pull up to 5v since the tx pin is 5v tolerant)

## Split comms

We can use half-duplex serial for cross board comms.[qmk
doc](https://docs.qmk.fm/#/serial_driver?id=usart-half-duplex)

Configurations needed:
* In your board’s halconf.h: `#define HAL_USE_SERIAL TRUE`
* In your board’s mcuconf.h: `#define STM32_SERIAL_USE_USARTn TRUE`

And this in `config.h`:

```
#define SOFT_SERIAL_PIN A15         // USART TX pin
#define SELECT_SOFT_SERIAL_SPEED 1 // or 0, 2, 3, 4, 5
                                   //  0: about 460800 baud
                                   //  1: about 230400 baud (default)
                                   //  2: about 115200 baud
                                   //  3: about 57600 baud
                                   //  4: about 38400 baud
                                   //  5: about 19200 baud
#define SERIAL_USART_DRIVER SD1    // USART driver of TX pin. default: SD1
#define SERIAL_USART_TX_PAL_MODE 7 // Pin "alternate function", see the respective datasheet for the appropriate values for your MCU. default: 7
#define SERIAL_USART_TIMEOUT 100 // USART driver timeout. default 100
```



## Connectors

TRRS jack is not hot-plug safe. However, having two usb-c conncetors on the
board is also suspectible to user error. 

## Code customization

Because Lalboard doesn't use switches, the matrix scan process is a bit 
different:

* High output needed to drive a row for scanning.
* Inverted result: low input means a column is active. 
[details on Transimpedance Amplifiers](https://hackaday.io/project/178232-lalboard/log/192192-phototransistors-transimpedance-amplifiers-and-response-times-oh-my)
* Low duty cycle: Since we drive the ir leds when scanning, the current during 
scanning can reach 50mA(10mA for each column). We want a low duty cycle to save
power.

### Matrix Scan

Seems like lite custom matrix (`CUSTOM_MATRIX = lite` [ref](https://docs.qmk.fm/#/custom_matrix?id=lite))
is not supported in split kb matrix scan code(quantum/split_common/matrix.c). 

We need to do full custom matrix code. However, we can still make use of the
helper functions for comms(`transport_{master|slave}`). The only part really
needs customization is the `read_cols_on_row()` method.


## Resistance choices

### Clusters: 
* 270 Ohm for regular ones.
* 750 Ohm for center

### Thumb
* R1: 120 
* R2: 270
* R3: 750 (a bit too weak 0.9v)
* R4: 750
* R5: 470 (left used 150 not changed and working)



