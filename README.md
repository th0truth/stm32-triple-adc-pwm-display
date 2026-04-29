# STM32 ADC Control

Firmware for an STM32F401CE-based board that reads three ADC inputs, drives three PWM outputs from those readings, and shows the selected ADC value on an 8-LED display.

The project is generated from STM32CubeMX and built with CMake using `arm-none-eabi-gcc`. Application code lives mainly in `Core/Src/main.c`; HAL, CMSIS, and FreeRTOS sources are kept in the generated `Drivers/` and `Middlewares/` directories.

## Features

- Samples three ADC1 channels with DMA:
  - ADC1_IN6 on PA6
  - ADC1_IN2 on PA2
  - ADC1_IN4 on PA4
- Maps ADC values to TIM1 PWM outputs:
  - TIM1_CH1 on PA8
  - TIM1_CH2 on PA9
  - TIM1_CH3 on PA10
- Displays the currently selected ADC channel on 8 GPIO LEDs.
- Supports two normal display modes:
  - bar graph
  - upper 8 bits in binary
- Supports a running LED animation whose speed follows the selected ADC value.
- Uses FreeRTOS tasks for button handling, display/PWM updates, and status activity.

## Hardware

Target MCU: `STM32F401CEUx`

| Function | Pin |
| --- | --- |
| Button input | PA0, active low with pull-up |
| ADC channel 0 | PA6 / ADC1_IN6 |
| ADC channel 1 | PA2 / ADC1_IN2 |
| ADC channel 2 | PA4 / ADC1_IN4 |
| PWM output 0 | PA8 / TIM1_CH1 |
| PWM output 1 | PA9 / TIM1_CH2 |
| PWM output 2 | PA10 / TIM1_CH3 |
| LED bit 0 | PC13 |
| LED bit 1 | PC15 |
| LED bit 2 | PA1 |
| LED bit 3 | PA3 |
| LED bit 4 | PA5 |
| LED bit 5 | PA7 |
| LED bit 6 | PB1 |
| LED bit 7 | PB10 |
| SWDIO | PA13 |
| SWCLK | PA14 |

## Button Controls

| Action | Behavior |
| --- | --- |
| Short press | Toggle display mode between bar graph and binary |
| Long press | Select the next ADC channel |
| Double short press | Toggle running LED mode |

When changing channels, the LED display blinks `channel + 1` times to show the selected ADC input.

## Build

Required tools:

- CMake 3.22 or newer
- Ninja
- Arm GNU toolchain with `arm-none-eabi-gcc` in `PATH`

Configure and build the debug firmware:

```sh
cmake --preset Debug
cmake --build --preset Debug
```

Build artifacts are written under `build/Debug/`. The main firmware image is:

```text
build/Debug/STM32_ADC_Control.elf
```

For a release build:

```sh
cmake --preset Release
cmake --build --preset Release
```

## Flash and Debug

The repository includes an OpenOCD configuration for ST-Link over SWD:

```sh
openocd -f openocd.cfg -c "program build/Debug/STM32_ADC_Control.elf verify reset exit"
```

For interactive debugging, start OpenOCD:

```sh
openocd -f openocd.cfg
```

Then connect with `arm-none-eabi-gdb`:

```sh
arm-none-eabi-gdb build/Debug/STM32_ADC_Control.elf
```

Inside GDB:

```gdb
target remote :3333
monitor reset halt
load
continue
```

## Project Layout

```text
Core/                       Application source and headers
Drivers/                    STM32 HAL and CMSIS drivers
Middlewares/Third_Party/    FreeRTOS source
cmake/stm32cubemx/          STM32CubeMX-generated CMake integration
CMakeLists.txt              Top-level CMake project
CMakePresets.json           Debug and Release build presets
STM32_ADC_Control.ioc       STM32CubeMX project file
openocd.cfg                 OpenOCD ST-Link/SWD configuration
```

## Better Project Name Ideas

The current name, `STM32_ADC_Control`, is accurate but broad. Names that better match the firmware behavior:

- `STM32 Analog LED PWM Controller`
- `STM32 ADC LED PWM Monitor`
- `F401 Analog Signal Visualizer`
- `STM32 Triple ADC PWM Display`
- `ADC PWM LED Console`
- `STM32 Analog IO Controller`

Best fit: **STM32 Triple ADC PWM Display**. It is specific to the three ADC inputs, PWM outputs, and LED display behavior implemented in the code.

## License

This project is licensed under the MIT License. See `LICENSE` for details.
