# STM3 NUCLEO-L476RG SCD41 CO2 Sensor Example

This project is an STM32 HAL firmware example for reading a Sensirion SCD41 CO2 sensor with a NUCLEO-L476RG development board.

The application uses Sensirion's official embedded SCD4x I2C driver and a project-specific STM32 HAL implementation of the Sensirion I2C abstraction layer. The firmware starts periodic measurements, checks when new data is ready, reads CO2, temperature, and relative humidity, then prints the values over UART.

## Features

- STM32L476RG based project
- Sensirion official SCD4x embedded I2C driver integration
- STM32 HAL port of `sensirion_i2c_hal.c`
- SCD41 communication over `I2C1`
- CO2, temperature, and relative humidity measurement
- UART serial output over `USART2`

## Hardware

- NUCLEO-L476RG development board
- Sensirion SCD41 CO2 sensor module
- USB cable for programming and UART serial output
- I2C pull-up resistors, if they are not already included on the sensor module

## Default Configuration

| Item | Value |
| --- | --- |
| MCU | STM32L476RG |
| Sensor | Sensirion SCD41 |
| Sensor I2C address | `0x62` |
| I2C peripheral | `I2C1` |
| I2C SCL | `PB8` |
| I2C SDA | `PB9` |
| UART peripheral | `USART2` |
| UART TX/RX | `PA2` / `PA3` |
| UART baud rate | `115200` |
| Measurement mode | Periodic measurement |
| Read interval | `5000 ms` |

## Wiring

Typical SCD41 wiring for the default firmware configuration:

| SCD41 Pin | NUCLEO-L476RG |
| --- | --- |
| VDD | 3.3 V |
| GND | GND |
| SCL | `PB8` / `I2C1_SCL` |
| SDA | `PB9` / `I2C1_SDA` |

Make sure the SCD41 and the STM32 board share a common ground.

## Project Structure

```text
Core/
  Inc/
    scd4x_i2c.h             Sensirion SCD4x driver interface
    sensirion_i2c.h         Sensirion I2C helper interface
    sensirion_i2c_hal.h     Platform HAL interface
    sensirion_common.h      Sensirion common utilities
    i2c.h                   STM32 I2C declarations
    usart.h                 STM32 UART declarations
    gpio.h                  STM32 GPIO declarations
  Src/
    main.c                  Main SCD41 measurement application
    scd4x_i2c.c             Sensirion SCD4x driver implementation
    sensirion_i2c.c         Sensirion I2C helper implementation
    sensirion_i2c_hal.c     STM32 HAL port for Sensirion I2C functions
    sensirion_common.c      Sensirion common utilities
    i2c.c                   I2C1 initialization
    usart.c                 USART2 initialization
    gpio.c                  GPIO initialization
Drivers/                    STM32 HAL and CMSIS drivers
Makefile                    Build and flash targets
STM32L476XX_FLASH.ld        Linker script
LED_BLINK_2.ioc             STM32CubeMX project configuration
```

## How It Works

1. Initializes STM32 HAL, system clock, GPIO, `USART2`, and `I2C1`.
2. Initializes the Sensirion I2C HAL layer with `sensirion_i2c_hal_init()`.
3. Configures the SCD4x driver for the SCD41 default I2C address, `0x62`.
4. Starts periodic measurement with `scd4x_start_periodic_measurement()`.
5. Every 5 seconds, checks `scd4x_get_data_ready_status()`.
6. When data is ready, reads the latest measurement with `scd4x_read_measurement()`.
7. Sends the result over UART.

## Example Serial Output

Open a serial monitor on the NUCLEO board's virtual COM port:

```text
Baud rate: 115200
Data bits: 8
Parity:    None
Stop bits: 1
```

Example output:

```text
CO2: 624 ppm | Temp: 24.531 C | RH: 45.217 %
CO2: 627 ppm | Temp: 24.544 C | RH: 45.193 %
```

If the sensor is not connected correctly or the I2C transaction fails, the firmware prints an error message such as:

```text
Data ready ERROR: -1
Read ERROR: -1
```

## Important Files Changed

- `Core/Src/main.c`: application logic for SCD41 periodic measurement and UART output.
- `Core/Src/sensirion_i2c_hal.c`: STM32 HAL implementation of Sensirion's platform-specific I2C read, write, and delay functions.

## Notes

- The SCD41 uses the SCD4x driver because it belongs to Sensirion's SCD4x sensor family.
- STM32 HAL expects the 7-bit I2C address shifted left by one bit; this is handled inside `sensirion_i2c_hal.c`.
- If you change the I2C peripheral or pins in STM32CubeMX, update the HAL implementation and wiring notes accordingly.
- Keep Sensirion's original license headers in the imported driver files when redistributing the project.
