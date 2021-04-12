---
title: "Zephyr"
date: 2021-03-13T11:37:15-08:00
draft: true
---

## Intro

![zephyr stack](https://www.zephyrproject.org/wp-content/uploads/sites/38/2020/03/Screen-Shot-2020-03-16-at-8.52.13-AM.png)
Zephyr is a OS for embedded systems. Developers can build their applications on top of Zephyr 
and flash them to boards. It greatly ease the development process.

## Setup


## Workflwow

* in WSL2 machine: `west build` output: `/build/zephyr/zephyr.hex`
* Flashing the hex using the stlink tool in Windows since WSL doesn't have USB access.


# Config files

## DTS/DTSI

Intro: https://docs.zephyrproject.org/latest/guides/dts/intro.html

DTSI defins the SOC's components, by specifying the name and their register address.

DTS defines the board by referecing to the nodes defined in above DTSI

### GPIO

Example of GPIO definition

```
// stm32f4.dtsi
 soc{
   // ...other configs.
		pinctrl: pin-controller@40020000 {
			compatible = "st,stm32-pinctrl";
			#address-cells = <1>;
			#size-cells = <1>;
			reg = <0x40020000 0x2000>;

      // GPIOA have 16 pins: PA0-PA15
			gpioa: gpio@40020000 {
				compatible = "st,stm32-gpio";
				gpio-controller;
				#gpio-cells = <2>;
				reg = <0x40020000 0x400>;
				clocks = <&rcc STM32_CLOCK_BUS_AHB1 0x00000001>;
				label = "GPIOA";
			};
```

Example of board definition file where LED is mapped to the SOC pin

```
// stm32f407-discover.dts
  // orange LED that is connected to PD13, e.g., GPIOD bit 13
	leds {
		compatible = "gpio-leds";
		orange_led_3: led_3 {
			gpios = <&gpiod 13 GPIO_ACTIVE_HIGH>;
			label = "User LD3";
		};
```

### GPIO and shields

When using shields, you can map GPIO pins between the shield header to the SOC pins. Example:

https://docs.zephyrproject.org/latest/guides/porting/shields.html#gpio-nexus-nodes
