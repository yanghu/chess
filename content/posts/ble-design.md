---
title: "Ble Design"
date: 2021-03-07T19:32:52-08:00
draft: true
---

# Wireless Keyboard Design

## Basics

I chose the popular chip among keyboard community: [nrf52840 SoC](https://www.nordicsemi.com/Products/Low-power-short-range-wireless/nRF52840). Since it is already well supported by ZMK, I won't have issues for firmware support. There's also plenty of refernce designs available, like nrfmicro.

The chip is essentially a MCU with wirless communication capabilities. Similar to other MCUs, you need to setup a circuit to make it work: capacitors for filtering power, crystals, etc. For wireless, you also need RF antenna. 

### SoC vs Module

Modules are available on the market, which embeds the SOC with the basic circuit and antenna in a package. 
It's more convenient and a bit more pricey. ($3 vs $6) The price difference is not too large for hobby 
projects though, due to the low volumn. 

However, for my keyboard, I decided to start from the SoC. The reason is simple: I don't want to solder the 
SMD myself. JLCPCB offers SMT assmebly, and they only have the SOC available.

### Overview

* nrf52 + supporting circuit == BLE module (like E73, holyiot)
* BLE module + power management circuit (charging, etc) == nrfMicro-like system.

## Building Nrf52 Circuit

### Reference designs

* Nordic's nrf reference PCBs: it has different power configurations.
* E73/holyiot's PCB schema
* nrfMicro: for power/charging circuits, as well as how to connect E73/nrf pins to the outer system.
* nrf52 dongle: PCB antenna for nrf52.

### Power Options

#### Main power

NRF52 has two options for main power regulator: LDO and DCDC. Since the input(VDD) is already behind an LDO(in the charging circuit to convert 6.6V to 3.3V), we can simply use LDO option inside the SOC for simplicity. This means:

* VDD connect to VDDH, which also connects to 3.3V supply.
* VDD -> Capacitor->Gnd (filtering)
* DCC left unconnected
* DEC4 -> Capacitor->GND (filtering)
* DEC4 ->DEC6. (required by datasheet)

#### USB power

We will use USB peripheral of this chip, so we need to configure USB power as well. 

* VBUS(USB bus 5V input) connects to 5V input. Also, connect a filter capacitor to GND.
* DECUSB(3.3V internal usb power) -> Capacitor -> GND (filtering)



## Antenna

### Impedanec matching

* We need to match impedance of load equal to the source, so max power transfer can occur
* When the cable is greater than lambda/8, it becomes a transmission line.
  * lambda = 300/f(mhz) meters
* All transmission line have charateristic impedance, that's a function of its inductance and capacitance
  * Z0=sqrt(L/C)
* The transmission line impedance should match the one of source and load.

### PCB size

* keep out area: 5mm from borad edge.
* extend 4.8mm from the copper spill
* 3mm long, 2mm width
* 3 "tooth" in total.
* track width: 10mil(0.01 in)