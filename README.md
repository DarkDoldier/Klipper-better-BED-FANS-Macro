# Klipper-better-BED-FANS-Macro

It´s based on the [macro](https://github.com/VoronDesign/VoronUsers/blob/main/printer_mods/Ellis/Bed_Fans/Klipper_Macros/bedfans.cfg) from [AndrewEllis93](https://github.com/AndrewEllis93). Now its kind of working like an active temperature control. I had the issue, that four fans heated the chamber to over 85C. That gave me severe clogging issues if I did not turn the fanspeed down manually to about 50% after reaching 65C. 

## Summary of Klipper Configuration Enhancements for Bed Fans and Chamber Temperature Control
I aimed to improve the management of bed fans and chamber temps so that it trys to hold a given desired temp. 

## My goals were:

- Automate fan speed adjustments based on both bed and chamber temperatures.
- Prevent overheating by kind of precisely controlling the chamber temperature.

# Key Changes:

**Separation of Fan Speed Variables:**

Introduced distinct variables for fan speeds during the bed heating process and for maintaining the chamber temperature:
variable_fast (0.8): Fan speed once the bed reaches the target temperature.
variable_slow (0.3): Fan speed while the bed is heating.
variable_fast_c (0.5): Fan speed for controlling the chamber temperature after the bed reaches the target.
variable_slow_c (0.1): Fan speed for maintaining chamber temperature once it reaches the target.

**Automatic Fan Control:**

The SET_HEATER_TEMPERATURE macro was enhanced to automatically manage fan speeds based on the bed and chamber temperatures, eliminating the need for manual intervention.
The bedfanloop monitors and adjusts fan speeds based on:
The bed’s proximity to its target temperature.
The chamber temperature in relation to the desired target (e.g., 65°C).
Avoiding Recursion Errors:
Removed the custom M140 macro override to prevent recursive calls, which had caused the heater and fans to fail to operate correctly.
Simplified the SET_HEATER_TEMPERATURE macro to directly set bed and extruder temperatures while controlling fan behavior.
Streamlined START_PRINT Macro:
With the automation in place, the manual command to start the bedfanloop was removed from the START_PRINT macro, simplifying the startup process.

# Implementation Steps:

**Before installing this you should have the bedfans setup and already running. I wont explain the steps here to get them installed.**

1. Download the BED_FANS_ACTIVE.cfg, load it up to your Printer and include it in your Printer.cfg by chaning

`[include befans.cfg]` (the original)

**to**

`[include BED_FANS_ACTIVE.cfg]`

2. Set up your fan accordingly - you should copy what you have had in your old bedfans.cfg.

`[fan_generic BedFans]
pin: # **PUT YOUR PIN HERE**
cycle_time: 0.04
kick_start_time: 0.25`

3. Set your variables. Copy what you already had. However here are three new variables - the c at the end stands for control. It will be used after you reached the desired temp for me 50% and 15% worked great. The last variable is the temp it should hold i set it to 65C:

`variable_threshold: 90        # If bed temp target is above this threshold, fans will be enabled. If temp is set to below this threshold, fans will be disabled.
variable_fast: 0.8            # Fan speed once bed temp is reached  
variable_slow: 0.3            # Fan speed while bed is heating
variable_fast_c: 0.5          # Fan speed for chamber temp control once bed temp is reached  
variable_slow_c: 0.15          # Fan speed for chamber temp control while bed is heating
variable_chamber_target: 65       # Target chamber temperature to maintain`

**Play with the settings. After you set it up you can test if it works by asking for the bed to heat up above the variable_threshold:. It should then, during the bed heating process, use the variable_slow and variable_fast. Once the bed is at temperature the chamber temperature control should kick in and the fans will switch to variable_slow_c and variable_fast_c to help maintain the chamber temperature more precisely**

