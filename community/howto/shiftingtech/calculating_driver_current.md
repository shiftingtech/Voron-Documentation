---
layout: default
title: Calculating Driver Current Settings
nav_exclude: true
---

# Calculating Driver Current Settings

When using most modern drivers, such as  the TMC2209, TMC2240, or TMC5160, the current is set in software.  In Klipper, the motor currents have two settings: run and hold.  [However, it is no longer recommended][klipperTMCUpdate] to specify a `hold_current` for most motors. 


Klipper current settings are based on root-mean-squared (RMS) and **not** on peak current.  Contrary to some old rumours, the spec sheets for most motors also RMS.

*however*, this does not mean it is a good idea to simply run every motor flat out.  In particular, any motor in a plastic mount will almost certainly melt that mount if you set it to its full, spec-sheet run current.

[klipperTMCUpdate]: https://www.klipper3d.org/TMC_Drivers.html#prefer-to-not-specify-a-hold_current

#### Calculating Currents

To estimate a maximum current setting for a given stepper, follow this process:

1. Look up the specifications for the stepper motor and locate the max current limits of the motor.
2. Multiply this value by 0.7.  With motors, we find this is high enough to deliver decent performance, but low enough not to melt anything.
3. Compare the value from step 2 with the Max ratings for your stepper driver. Choose the LOWER of the two values.  For TMC2209s in the step stick format, we recommend using a maximum of 1.4A:  Although the TMC2209 chip is rated at 2A, and some manufacturers list their step sticks the same way, we have found that the cooling is generally not sufficient, and a lower value is required.  For other drivers, please see the manufacturer spec sheet.
4. If you plan to set a separate hold current, multiply the maximum run current by 0.6 to determine the hold current.  (Typically round to the nearest 0.05)


#### Example

The LDO 42STH130-1684 is specified with a maximum current of 1.68 Amps.  Maximum run current is 1.68 * .7 = 1.176, rounded to a maximum run current of 1.2 Amps.  Maximum hold current is 1.2 * 0.6 = 0.72, rounded to a hold current of 0.7 Amps. 

### Testing

Many motors perform quite well at lower currents, so we recommend erring low first:  if you start too low, you'll probably just have some skipped steps.  If you start too high, you risk melting things.