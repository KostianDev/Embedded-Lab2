# Embedded Lab 2 - LED Garland (STM32F407)

STM32F407 project that drives four LEDs using four different blinking algorithms. The firmware was generated with STM32CubeMX and is built with the provided Makefile. Button-driven algorithm switching uses EXTI with debounce, and LED timing is fully non-blocking (tick-based state machines).

## Hardware
- Processor: STM32F407VGT6
- LEDs: PE7 (Red), PE9 (Yellow), PE11 (Blue), PE13 (Green)
- User button: PA0 (EXTI0), pull-down, rising-edge trigger

## Build
```bash
make
```

## Flash
Use any preferred tool to program the target. Examples:

- STM32CubeProgrammer (CLI):
```bash
STM32_Programmer_CLI -c port=SWD -w build/Lab2.bin 0x08000000 -v -rst
```

- stlink (st-flash):
```bash
st-flash --reset write build/Lab2.bin 0x08000000
```

## Run
After programming, the firmware starts with Algorithm 0. Press the user button (PA0) to cycle through algorithms.

### LED algorithms
- Alg_0 (250 ms step): single LED rotates
  - Red --> Yellow --> Blue --> Green
- Alg_1 (250 ms step): adjacent pairs
  - (Red+Yellow) --> (Yellow+Blue) --> (Blue+Green) --> (Red+Green)
- Alg_2 (500 ms step): all blink
  - All ON --> All OFF
- Alg_3 (500 ms step): single LED in another order
  - Red --> Green --> Yellow --> Blue

## Implementation notes
- Non-blocking timing: All algorithms are implemented as small state machines driven by `HAL_GetTick()` (no blocking `HAL_Delay()` in the main loop paths).
- Instant switching: On button press, the current algorithm step and timer are reset so the next algorithm starts immediately.
- Debounce: EXTI0 handler applies a 150 ms software debounce window to suppress bounce-induced multiple toggles.

## Signal traces (logic analyzer)
Captured traces for the four LED lines and the button are included:
- `Sigrok.sr` - session file (PulseView / sigrok)
- `Sigrok.pvs` - PulseView view settings
- `Trace_Lab2.png` - screenshot of the captured waveforms

To inspect:
1. Open `Sigrok.sr` in PulseView.
2. Optionally load `Sigrok.pvs` to restore the preconfigured view.
3. Observe LED channels (Red/Yellow/Blue/Green) across algorithm changes (trigger with the button channel).