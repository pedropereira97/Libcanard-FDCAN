# Libcanard Driver for STM32H7 Microcontrollers (based on https://github.com/UAVCAN/platform_specific_components.git )

This is a compact and simple driver for STM32H7 microcontrollers.
It is based on https://github.com/UAVCAN/platform_specific_components.git 
implementation of a STM32 driver for microcontrollers with bxCAN.

So far this driver has been tested at least with the following MCU:

* STM32H743 - both FDCAN1 and FDCAN2 simultaneously.

## Features

* Works with FreeRTOS and on bare metal.
* Compact, suitable for ROM and RAM limited applications (e.g. bootloaders).
* Does not use IRQ and critical sections at all.
* Non-blocking API.
* Supports both CAN1 and CAN2 at the same time.
* Supports hardware acceptance filters.

## Caveats

Some design trade-offs have been made in order to provide the above features.

* The RX FIFO is only 32 CAN frames deep,
since this is the depth provided by the CAN hardware.
In order to avoid frame loss due to RX overrun,
the following measures should be adopted:
  * Use hardware acceptance filters - the driver
provides a convenient API to configure them.
  * Read the queue at least every 32x minimum frame transmission intervals.
* The driver does not permit concurrent access from different threads of execution.
* The clocks of the CAN peripheral must be enabled by the application
before the driver is initialized.

Note that it is possible to invoke the driver's API functions from IRQ context,
provided that the application takes care about proper guarding with critical sections.
This approach allows the application to read the RX queue from an external CAN IRQ handler.
