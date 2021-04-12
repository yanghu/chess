---
title: "Keyboard Pcb Design"
date: 2021-04-11T18:37:25-07:00
draft: true
---

# Main Parts and Choices

The keyboard circuit has the following essential parts:
* Voltage regulator: converts USB 5v to 3.3v used by MCUs
* MCU: The process that controls the whole board.
* IO Expander: to scan switches on secondary side and communicate with the main
side via SPI.

# Component choices

Use this [site](https://yaqwsx.github.io/jlcparts/#/) to search JLCPCB parts
for SMD assembly service availability and price.

Summary:

| Component  |  Model   | JLCPCB PN |  Notes |
|------------|----------|-----------|--------|
| MCU        |STM32F072CBU6| C92504  |        |
| Regulator  |XC6206P332MR| C5446   | 3.3V fixed output|
| BJT        |MMBT3904    | C20526  | need add resistors|
| diode      | 1N4148     |C81598  | for both switches and reset button.|


## MCU

I'm using STM32F072CB, which is more powerful than the atmega on pro micros.
The atmega32u4 is 8 bit processor and only has 32K memory size. In comparison,
the Arm chips are 32bit, and has more program memory(F072C8 has 64K, F072CB has
128K). The same chip is used in other custom keyboard designs like Ferris.

The model I'm using is STM32F072CBU6, which is the only one in stock in JLCPCB.
The chip seems to be out of stock everywhere at this moment.

## Reset button circuit

To reset(and enter bootloader), we need to trigger both NRST and BOOT0 pins.
This [guide](http://acheronproject.com/reset_article/principle.html#conclusion)
suggested 3 variants and I'm using the middle one for simplicity. For this 
circuit, we need a transistor. I'm not using the prebiased one shown there 
(50V, 100mA, 2.2kOhm and 47kOhm) since it's extended part and has $3 extra 
charge. Instead I'm using a 40V, 200mA NPN transistor and adding external 
resistors.

## Major parts
