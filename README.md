# My Customisation
## Printer Hardware
 - Ender 3 Pro
 - BTT SKR Mini E3 v3
 - BTT TFT35 V3
 - BTT Smart Filament Runout Sensor
 - Creality BL-Touch v3.1
 - NeoPixel LEDs x 7
 - Raspberry Pi4 + OctoPrint + Night-light Pi-Cam
## Software for builds
 - Visual Studio Code + PlatformIO + Auto Marlin Builder
 - Github Desktop for Windows
## Fix for not flashing new firmware on new board (August stock firmware)
When my v3 board arrived then fitted the board work fine, but when it came time to upgrade the firmware, for some reason no amount things I tried with the same SD card I was already using with the v1.2 would flash. The printer simply started normally igoring the new firmware - I tried both my own custom firmware and the newer stock versions from BTT. This is what I did to fix the problem:
- Get the stock version of the latest firmware from BTT
- Using an SD card 4GB or lower, format it to `FAT` not `FAT32`
- Copy the new firmware to the SD card
- Flash the firmware
This then worked for me and I then tried the same with my custom firmware and it to flashed fine. Afterwards I was even able to format the card back to `FAT32` and it was still able to read from it.
## Marlin Configurations (work in progress)
 - `platformio.ini`
   - default environment: `STM32G0B1RE_btt`
 - `Marlin/Version.h`
   - Get rid of "bugfix" from build version: `"v2.0.x"`
   - Set build date to date/time firmware was compiled: `__DATE__ " " __TIME__`
 - `Marlin/_Bootscreen.h`
   - Copied from Ender 3 Pro configuration archive
 - `Marlin/_Statusscreen.h`
   - Copied from Ender 3 Pro configuration archive
 - `Marlin/src/HAL/STM32F1/inc/SanityCheck.h`
   - Disable probe margin checks to allow for greater coverage of bed
   - Commented out error `PROBING_MARGIN_BACK must be >= 0`
   - Commented out error `PROBING_MARGIN_FRONT must be >= 0`
   - Commented out error `PROBING_MARGIN_LEFT must be >= 0`
   - Commented out error `PROBING_MARGIN_RIGHT must be >= 0`
 - `Marlin/Configuration.h`
   - Set author to `"(Taomyn, for SKR-mini-E3-V3.0)"`
   - Enable `CUSTOM_VERSION_FILE`
   - Enable `SHOW_CUSTOM_BOOTSCREEN`
   - Enable `CUSTOM_STATUS_SCREEN_IMAGE`
   - Set `MOTHERBOARD BOARD_BTT_SKR_MINI_E3_V3_0`
   - Set `SERIAL_PORT 2`
   - Set `BAUDRATE 250000`
   - Enable `BAUD_RATE_GCODE`
   - Enable `SERIAL_PORT_2`
   - Enable `BAUDRATE_2`
   - Set `CUSTOM_MACHINE_NAME "Ender-3 Pro"`
   - Set `TEMP_SENSOR_BED 1`
   - Set `HEATER_0_MAXTEMP 315` to allow for some PETG filaments
   - Enable `PID_EDIT_MENU`
   - Enable `PID_AUTOTUNE_MENU`
   - Set `DEFAULT_Kp`, `DEFAULT_Ki` and `DEFAULT_Kd` to recent PID tune values to save repeating when flashed
   - Enable `PIDTEMPBED`
   - Set `DEFAULT_bedKp`, `DEFAULT_bedKi` and `DEFAULT_bedKd` to recent PID bed tune values to save repeating when flashed
   - Set `EXTRUDE_MINTEMP` to '180' as 170 seems too low
   - Set `EXTRUDE_MAXLENGTH` to '600'
   - Disable `USE_ZMIN_PLUG` as using BL-Touch and Z endstop not fitted
   - Set X, Y, Z and E0 driver types to 'TMC2209'
   - Set `DEFAULT_AXIS_STEPS_PER_UNIT` to recent calibration values to save repating when flashed
   - Set `DEFAULT_MAX_FEEDRATE`, `DEFAULT_MAX_ACCELERATION`, `DEFAULT_ACCELERATION`, `DEFAULT_RETRACT_ACCELERATION`, `DEFAULT_TRAVEL_ACCELERATION`, `JUNCTION_DEVIATION_MM` to more suitable values
   - Enable `S_CURVE_ACCELERATION`
   - Disable `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` Z endstop not fitted
   - Enable `USE_PROBE_FOR_Z_HOMING` for some reason needs this to force using the BL-Touch on Z homing
   - Enable `BLTOUCH`
   - Set `NOZZLE_TO_PROBE_OFFSET` to correct values of BL-Touch installed
   - Set `PROBING_MARGIN 0`
   - Set `XY_PROBE_FEEDRATE (166*60)`
   - Set `Z_PROBE_FEEDRATE_FAST (10*60)`
   - Set `MULTIPLE_PROBING 2`
   - Set `Z_CLEARANCE_DEPLOY_PROBE 5`
   - Set `Z_CLEARANCE_BETWEEN_PROBES 4`
   - Set `Z_CLEARANCE_MULTI_PROBE 4`
   - Enable `Z_AFTER_PROBING`
   - Enable `Z_MIN_PROBE_REPEATABILITY_TEST`
   - Set `PROBING_BED_TEMP 60`
   - Set `INVERT_X_DIR true` and `INVERT_E0_DIR true` - correct for Ender 3 Pro
   - Set `Z_AFTER_HOMING 20 ` - leaves more room to see between nozzle and bed
   - Set `X_BED_SIZE 235` and `Y_BED_SIZE 235` - correct for Ender 3 Pro
   - Set `X_MAX_POS (X_BED_SIZE+15)` - allows for bed level probing further out
   - Set `Z_MAX_POS 250` - correct for Ender 3 Pro
   - Enable `FILAMENT_RUNOUT_SENSOR`
   - Set `FILAMENT_RUNOUT_SENSOR_DEBUG` - useful for seeing distance info in terminal output
   - Set `FILAMENT_RUNOUT_DISTANCE_MM 25` - BTT recommended 7mm is too short causing false alarms
   - Enable `FILAMENT_MOTION_SENSOR`
   - Enable `AUTO_BED_LEVELING_BILINEAR`
   - Enable `RESTORE_LEVELING_AFTER_G28`
   - Enable `PREHEAT_BEFORE_LEVELING`
   - Set `LEVELING_NOZZLE_TEMP 0` never gets close to bed so no need to heat it up
   - Set `LEVELING_BED_TEMP 60` make sure bed is heated to average temp when printing
   - Enable `DEBUG_LEVELING_FEATURE` - as we have the RAM this is useful
   - Enable `G26_MESH_VALIDATION` - must test this
   - Set `GRID_MAX_POINTS_X 5` - 5 x 5 grid is more accurate
   - Enable `EXTRAPOLATE_BEYOND_GRID`
   - Enable `ABL_BILINEAR_SUBDIVISION`
   - Enable `LCD_BED_LEVELING`
   - Enable `MESH_EDIT_MENU`
   - Enable `LEVEL_BED_CORNERS`
   - Set `LEVEL_CORNERS_HEIGHT 0.1` - ***need to find out why this was recommended***
   - Enable `LEVEL_CENTER_TOO`
   - Enable `Z_SAFE_HOMING`
   - Set `HOMING_FEEDRATE_MM_M { (50*60), (50*60), (10*60) }` increased Z speed
   - Enable `EEPROM_SETTINGS`
   - Customised `Preheat Constants` to personal preferences, replaced ABS with PETG
   - Enable `NOZZLE_PARK_FEATURE`
   - Enable `PRINTCOUNTER`
   - Set `PRINTCOUNTER_SAVE_INTERVAL 10`
   - Set `DISPLAY_CHARSET_HD44780 WESTERN`
   - Enable `SDSUPPORT`
   - Enable `SD_CHECK_AND_RETRY`
   - Enable `INDIVIDUAL_AXIS_HOMING_MENU`
   - Enable `INDIVIDUAL_AXIS_HOMING_SUBMENU`
   - Enable `CR10_STOCKDISPLAY`
   - Enable `FAN_SOFT_PWM`
   - Enable `NEOPIXEL_LED`
   - Set `NEOPIXEL_TYPE NEO_GRB` - specfic to the LEDs I used
   - Set `NEOPIXEL_PIXELS 7` - I have 7 LED strip connected
   - Set `NEOPIXEL_BRIGHTNESS 255`
   - Enable `NEOPIXEL_STARTUP_TEST`
 - `Marlin/Configuration_adv.h`
   - Set `WATCH_TEMP_PERIOD 40`
   - Set `WATCH_BED_TEMP_PERIOD 90`
   - Enable `HOTEND_IDLE_TIMEOUT`
   - Set `HOTEND_IDLE_TIMEOUT_SEC (10*60)`
   - Enable `USE_CONTROLLER_FAN`
   - Set `CONTROLLER_FAN_PIN PB15`
   - Enable `CONTROLLER_FAN_EDITABLE`
   - Set `E0_AUTO_FAN_PIN PC7`
   - Enable `CASE_LIGHT_ENABLE`
   - Set `CASE_LIGHT_PIN 0`
   - Set `CASE_LIGHT_DEFAULT_BRIGHTNESS 255`
   - Enable `CASE_LIGHT_MENU`
   - Enable `CASE_LIGHT_USE_NEOPIXEL` - use the same LED strip as the main ones
   - Enable `QUICK_HOME`
   - Set `SLOWDOWN_DIVISOR 8` - ***need to find out why this was recommended***
   - Enable `XY_FREQUENCY_LIMIT`
   - Enable `ADAPTIVE_STEP_SMOOTHING`
   - Enable `BEEP_ON_FEEDRATE_CHANGE`
   - Enable `PROBE_OFFSET_WIZARD`
   - Enable `PROBE_OFFSET_WIZARD_START_Z`
   - Set `PROBE_OFFSET_WIZARD_XY_POS { X_CENTER + 40, Y_CENTER }` - pushes probe back to centre of bed
   - Enable `LCD_INFO_MENU`
   - Enable `TURBO_BACK_MENU_ITEM`
   - Enable `LED_CONTROL_MENU`
   - Set `LED_USER_PRESET_GREEN 255` and `LED_USER_PRESET_BLUE 255` - correct for my LEDs
   - Enable `LED_USER_PRESET_STARTUP`
   - Set `LCD_TIMEOUT_TO_STATUS 30000` - stops it dimming too quickly
   - Enable `SOUND_MENU_ITEM`
   - Disable `BOOT_MARLIN_LOGO_SMALL`
   - Enable `STATUS_MESSAGE_SCROLLING`
   - Enable `LCD_DECIMAL_SMALL_XY`
   - Enable `LCD_SET_PROGRESS_MANUALLY`
   - Enable `SHOW_REMAINING_TIME`
   - Enable `USE_M73_REMAINING_TIME`
   - Enable `ROTATE_PROGRESS_DISPLAY`
   - Enable `PRINT_PROGRESS_SHOW_DECIMALS`
   - Enable `LCD_PROGRESS_BAR`
   - Enable `LCD_PROGRESS_BAR_TEST`
   - Enable `SD_IGNORE_AT_STARTUP`
   - Enable `GCODE_REPEAT_MARKERS`
   - Enable `BROWSE_MEDIA_ON_INSERT`
   - Enable `SDCARD_SORT_ALPHA`
   - Set `SDSORT_LIMIT 255`
   - Set `SDSORT_GCODE true`
   - Set `SDSORT_USES_RAM true`
   - Set `SDSORT_CACHE_NAMES true`
   - Enable `UTF_FILENAME_SUPPORT`
   - Enable `LONG_FILENAME_HOST_SUPPORT`
   - Enable `LONG_FILENAME_WRITE_SUPPORT` - currently disabled as it fails to compile
   - Enable `SCROLL_LONG_FILENAMES`
   - Enable `SD_ABORT_ON_ENDSTOP_HIT`
   - Enable `AUTO_REPORT_SD_STATUS`
   - Set `SDCARD_CONNECTION ONBOARD` - correct for BTT SKR Mini E3 v3.0
   - Enable `XYZ_NO_FRAME`
   - Disable `XYZ_HOLLOW_FRAME`
   - Disable `STATUS_CHAMBER_ANIM`
   - Enable `STATUS_ALT_BED_BITMAP` and `STATUS_ALT_FAN_BITMAP` - I think they look better
   - Set `STATUS_FAN_FRAMES 4`
   - Enable `BOOT_MARLIN_LOGO_ANIMATED`
   - Enable `BABYSTEPPING`
   - Set `BABYSTEP_MULTIPLICATOR_Z  4`
   - Enable `DOUBLECLICK_FOR_Z_BABYSTEPPING`
   - Enable `BABYSTEP_ZPROBE_OFFSET`
   - Enable `BABYSTEP_ZPROBE_GFX_OVERLAY` - reminds you which way to turn for adjustment
   - Enable `PROBING_MARGIN_LEFT`, `PROBING_MARGIN_RIGHT`, `PROBING_MARGIN_FRONT`, `PROBING_MARGIN_BACK` - to avoid clips holding bed
   - Set `PROBING_MARGIN_xxxx nn` - to avoid clips holding bed
   - Set `BLOCK_BUFFER_SIZE 32`, `BUFSIZE 32` and `TX_BUFFER_SIZE 32` - better USB comms performance
   - Enable `BEZIER_CURVE_SUPPORT`
   - Enable `EMERGENCY_PARSER`
   - Enable `ADVANCED_OK`
   - Enable `SERIAL_FLOAT_PRECISION`
   - Enable `ADVANCED_PAUSE_FEATURE`
   - Set `FILAMENT_CHANGE_UNLOAD_FEEDRATE 100`
   - Set `FILAMENT_CHANGE_UNLOAD_LENGTH 600`
   - Set `FILAMENT_CHANGE_SLOW_LOAD_FEEDRATE 10`
   - Set `FILAMENT_CHANGE_SLOW_LOAD_LENGTH 25`
   - Set `FILAMENT_CHANGE_FAST_LOAD_FEEDRATE 10`
   - Set `FILAMENT_CHANGE_FAST_LOAD_LENGTH 300`
   - Set `ADVANCED_PAUSE_CONTINUOUS_PURGE`
   - Enable `PARK_HEAD_ON_PAUSE`
   - Enable `FILAMENT_LOAD_UNLOAD_GCODES`
   - Set `X_CURRENT 580`, `Y_CURRENT 580`, `Z_CURRENT 580` and `E0_CURRENT 650` - correct for Ender 3 Pro
   - Set `X_CURRENT_HOME X_CURRENT /2`
   - Set `Y_CURRENT_HOME Y_CURRENT /2`
   - Set `Z_CURRENT_HOME Z_CURRENT /2`
   - Set `CHOPPER_TIMING CHOPPER_DEFAULT_24V`- correct for Ender 3 Pro with 24v PSU
   - Enable `MONITOR_DRIVER_STATUS`
   - Set `Z_HYBRID_THRESHOLD 20`
   - Set `X_STALL_SENSITIVITY 72`
   - Set `Y_STALL_SENSITIVITY 70`
   - Set `Z_STALL_SENSITIVITY 10`
   - Enable `IMPROVE_HOMING_RELIABILITY`
   - Enable `SQUARE_WAVE_STEPPING`
   - Enable `TMC_DEBUG`
   - Enable `M115_GEOMETRY_REPORT`
   - Enable `M114_DETAIL`
   - Enable `M114_REALTIME`
   - Enable `REPORT_FAN_CHANGE`
   - Enable `GCODE_QUOTED_STRINGS`
   - Enable `GCODE_CASE_INSENSITIVE` - personal hate of mine
   - Enable `HOST_ACTION_COMMANDS`
   - Enable `HOST_PAUSE_M76`
   - Enable `HOST_PROMPT_SUPPORT`
   - Enable `HOST_STATUS_NOTIFICATIONS`
   - Enable `CANCEL_OBJECTS`

# Marlin 3D Printer Firmware

![GitHub](https://img.shields.io/github/license/marlinfirmware/marlin.svg)
![GitHub contributors](https://img.shields.io/github/contributors/marlinfirmware/marlin.svg)
![GitHub Release Date](https://img.shields.io/github/release-date/marlinfirmware/marlin.svg)
[![Build Status](https://github.com/MarlinFirmware/Marlin/workflows/CI/badge.svg?branch=bugfix-2.0.x)](https://github.com/MarlinFirmware/Marlin/actions)

<img align="right" width=175 src="buildroot/share/pixmaps/logo/marlin-250.png" />

Additional documentation can be found at the [Marlin Home Page](https://marlinfw.org/).
Please test this firmware and let us know if it misbehaves in any way. Volunteers are standing by!

## Marlin 2.0 Bugfix Branch

__Not for production use. Use with caution!__

Marlin 2.0 takes this popular RepRap firmware to the next level by adding support for much faster 32-bit and ARM-based boards while improving support for 8-bit AVR boards. Read about Marlin's decision to use a "Hardware Abstraction Layer" below.

This branch is for patches to the latest 2.0.x release version. Periodically this branch will form the basis for the next minor 2.0.x release.

Download earlier versions of Marlin on the [Releases page](https://github.com/MarlinFirmware/Marlin/releases).

## Building Marlin 2.0

To build Marlin 2.0 you'll need [Arduino IDE 1.8.8 or newer](https://www.arduino.cc/en/main/software) or [PlatformIO](https://docs.platformio.org/en/latest/ide.html#platformio-ide). We've posted detailed instructions on [Building Marlin with Arduino](https://marlinfw.org/docs/basics/install_arduino.html) and [Building Marlin with PlatformIO for ReArm](https://marlinfw.org/docs/basics/install_rearm.html) (which applies well to other 32-bit boards).

## Hardware Abstraction Layer (HAL)

Marlin 2.0 introduces a layer of abstraction so that all the existing high-level code can be built for 32-bit platforms while still retaining full 8-bit AVR compatibility. Retaining AVR compatibility and a single code-base is important to us, because we want to make sure that features and patches get as much testing and attention as possible, and that all platforms always benefit from the latest improvements.

### Current HALs

  #### AVR (8-bit)

  board|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Arduino AVR](https://www.arduino.cc/)|ATmega, ATTiny, etc.|16-20MHz|64-256k|2-16k|5V|no

  #### DUE

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Arduino Due](https://www.arduino.cc/en/Guide/ArduinoDue), [RAMPS-FD](https://www.reprap.org/wiki/RAMPS-FD), etc.|[SAM3X8E ARM-Cortex M3](https://www.microchip.com/wwwproducts/en/ATsam3x8e)|84MHz|512k|64+32k|3.3V|no

  #### ESP32

  board|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [ESP32](https://www.espressif.com/en/products/hardware/esp32/overview)|Tensilica Xtensa LX6|160-240MHz variants|---|---|3.3V|---

  #### LPC1768 / LPC1769

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Re-ARM](https://www.kickstarter.com/projects/1245051645/re-arm-for-ramps-simple-32-bit-upgrade)|[LPC1768 ARM-Cortex M3](https://www.nxp.com/products/microcontrollers-and-processors/arm-based-processors-and-mcus/lpc-cortex-m-mcus/lpc1700-cortex-m3/512kb-flash-64kb-sram-ethernet-usb-lqfp100-package:LPC1768FBD100)|100MHz|512k|32+16+16k|3.3-5V|no
  [MKS SBASE](https://reprap.org/forum/read.php?13,499322)|LPC1768 ARM-Cortex M3|100MHz|512k|32+16+16k|3.3-5V|no
  [Selena Compact](https://github.com/Ales2-k/Selena)|LPC1768 ARM-Cortex M3|100MHz|512k|32+16+16k|3.3-5V|no
  [Azteeg X5 GT](https://www.panucatt.com/azteeg_X5_GT_reprap_3d_printer_controller_p/ax5gt.htm)|LPC1769 ARM-Cortex M3|120MHz|512k|32+16+16k|3.3-5V|no
  [Smoothieboard](https://reprap.org/wiki/Smoothieboard)|LPC1769 ARM-Cortex M3|120MHz|512k|64k|3.3-5V|no

  #### SAMD51

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Adafruit Grand Central M4](https://www.adafruit.com/product/4064)|[SAMD51P20A ARM-Cortex M4](https://www.microchip.com/wwwproducts/en/ATSAMD51P20A)|120MHz|1M|256k|3.3V|yes

  #### STM32F1

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Arduino STM32](https://github.com/rogerclarkmelbourne/Arduino_STM32)|[STM32F1](https://www.st.com/en/microcontrollers-microprocessors/stm32f103.html) ARM-Cortex M3|72MHz|256-512k|48-64k|3.3V|no
  [Geeetech3D GTM32](https://github.com/Geeetech3D/Diagram/blob/master/Rostock301/Hardware_GTM32_PRO_VB.pdf)|[STM32F1](https://www.st.com/en/microcontrollers-microprocessors/stm32f103.html) ARM-Cortex M3|72MHz|256-512k|48-64k|3.3V|no

  #### STM32F4

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [STEVAL-3DP001V1](https://www.st.com/en/evaluation-tools/steval-3dp001v1.html)|[STM32F401VE Arm-Cortex M4](https://www.st.com/en/microcontrollers-microprocessors/stm32f401ve.html)|84MHz|512k|64+32k|3.3-5V|yes

  #### Teensy++ 2.0

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Teensy++ 2.0](https://www.microchip.com/wwwproducts/en/AT90USB1286)|[AT90USB1286](https://www.microchip.com/wwwproducts/en/AT90USB1286)|16MHz|128k|8k|5V|no

  #### Teensy 3.1 / 3.2

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Teensy 3.2](https://www.pjrc.com/store/teensy32.html)|[MK20DX256VLH7](https://www.mouser.com/ProductDetail/NXP-Freescale/MK20DX256VLH7) ARM-Cortex M4|72MHz|256k|32k|3.3V-5V|yes

  #### Teensy 3.5 / 3.6

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Teensy 3.5](https://www.pjrc.com/store/teensy35.html)|[MK64FX512VMD12](https://www.mouser.com/ProductDetail/NXP-Freescale/MK64FX512VMD12) ARM-Cortex M4|120MHz|512k|192k|3.3-5V|yes
  [Teensy 3.6](https://www.pjrc.com/store/teensy36.html)|[MK66FX1M0VMD18](https://www.mouser.com/ProductDetail/NXP-Freescale/MK66FX1M0VMD18) ARM-Cortex M4|180MHz|1M|256k|3.3V|yes

  #### Teensy 4.0 / 4.1

  boards|processor|speed|flash|sram|logic|fpu
  ----|---------|-----|-----|----|-----|---
  [Teensy 4.0](https://www.pjrc.com/store/teensy40.html)|[IMXRT1062DVL6A](https://www.mouser.com/new/nxp-semiconductors/nxp-imx-rt1060-crossover-processor/) ARM-Cortex M7|600MHz|1M|2M|3.3V|yes
  [Teensy 4.1](https://www.pjrc.com/store/teensy41.html)|[IMXRT1062DVJ6A](https://www.mouser.com/new/nxp-semiconductors/nxp-imx-rt1060-crossover-processor/) ARM-Cortex M7|600MHz|1M|2M|3.3V|yes

## Submitting Patches

Proposed patches should be submitted as a Pull Request against the ([bugfix-2.0.x](https://github.com/MarlinFirmware/Marlin/tree/bugfix-2.0.x)) branch.

- This branch is for fixing bugs and integrating any new features for the duration of the Marlin 2.0.x life-cycle.
- Follow the [Coding Standards](https://marlinfw.org/docs/development/coding_standards.html) to gain points with the maintainers.
- Please submit Feature Requests and Bug Reports to the [Issue Queue](https://github.com/MarlinFirmware/Marlin/issues/new/choose). Support resources are also listed there.
- Whenever you add new features, be sure to add tests to `buildroot/tests` and then run your tests locally, if possible.
  - It's optional: Running all the tests on Windows might take a long time, and they will run anyway on GitHub.
  - If you're running the tests on Linux (or on WSL with the code on a Linux volume) the speed is much faster.
  - You can use `make tests-all-local` or `make tests-single-local TEST_TARGET=...`.
  - If you prefer Docker you can use `make tests-all-local-docker` or `make tests-all-local-docker TEST_TARGET=...`.

### [RepRap.org Wiki Page](https://reprap.org/wiki/Marlin)

## Credits

The current Marlin dev team consists of:

 - Scott Lahteine [[@thinkyhead](https://github.com/thinkyhead)] - USA &nbsp; [Donate](https://www.thinkyhead.com/donate-to-marlin) / Flattr: [![Flattr Scott](https://api.flattr.com/button/flattr-badge-small.png)](https://flattr.com/submit/auto?user_id=thinkyhead&url=https://github.com/MarlinFirmware/Marlin&title=Marlin&language=&tags=github&category=software)
 - Roxanne Neufeld [[@Roxy-3D](https://github.com/Roxy-3D)] - USA
 - Chris Pepper [[@p3p](https://github.com/p3p)] - UK
 - Bob Kuhn [[@Bob-the-Kuhn](https://github.com/Bob-the-Kuhn)] - USA
 - João Brazio [[@jbrazio](https://github.com/jbrazio)] - Portugal
 - Erik van der Zalm [[@ErikZalm](https://github.com/ErikZalm)] - Netherlands &nbsp; [![Flattr Erik](https://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=ErikZalm&url=https://github.com/MarlinFirmware/Marlin&title=Marlin&language=&tags=github&category=software)

## License

Marlin is published under the [GPL license](/LICENSE) because we believe in open development. The GPL comes with both rights and obligations. Whether you use Marlin firmware as the driver for your open or closed-source product, you must keep Marlin open, and you must provide your compatible Marlin source code to end users upon request. The most straightforward way to comply with the Marlin license is to make a fork of Marlin on Github, perform your modifications, and direct users to your modified fork.

While we can't prevent the use of this code in products (3D printers, CNC, etc.) that are closed source or crippled by a patent, we would prefer that you choose another firmware or, better yet, make your own.
