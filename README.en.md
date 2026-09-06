# Lotmaxx Shark V2 Reference

This is a field reference for the **Lotmaxx Shark V2** with the **Beta M V1.1** controller and the stock laser module. It records verified wiring, a reproducible Marlin build path, and the LightBurn compatibility layer.

## Verified facts

- MCU: STM32F407VGT6, LQFP100; board marking: Beta M V1.1, `20201207`.
- Laser driver: SC-60-V2-L-V1.0.
- Laser-related MCU signals: `PE9` powers the driver rail, `PE12` enables the board-side low-side cascade, `PE13` is laser PWM, and `PD9` is the active-low focus switch.
- LightBurn's Marlin profile uses `M106 S0..255` for laser power. Power changes must be synchronized with motion, otherwise rapid moves between vectors can burn lines.
- Stock controller firmware `SC-10Shark-v1.6` plus the stock DWIN resources are the only fully verified stable 3D-print configuration.

Read the Russian documentation for the full pin tables, build instructions, safety limits, DWIN status, and LightBurn setup:

- [Status and limits](docs/STATUS.md)
- [Controller board](docs/BOARD.md)
- [Laser module](docs/LASER.md)
- [Building Marlin](docs/MARLIN.md)
- [LightBurn](docs/LIGHTBURN.md)

This repository intentionally excludes experimental firmware binaries, DWIN packages, and thermal-protection bypasses.
