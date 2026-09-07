# STM32F407VET6: процессор и выводы Beta M V1.1

## Идентификация

| Поле | Значение |
|---|---|
| Микроконтроллер | `STM32F407VET6` |
| Серия | STM32F407, Arm Cortex-M4F |
| Корпус | LQFP100, 14 x 14 мм |
| Flash | 512 KiB (`E` в маркировке) |
| SRAM | 192 KiB основной SRAM + 4 KiB backup SRAM; из основной есть 64 KiB CCM |
| Плата | Beta M V1.1, `20201207` |
| Официальная документация | [ST product page](https://www.st.com/en/microcontrollers-microprocessors/stm32f407ve.html), [datasheet DS8626 Rev 12](https://www.st.com/resource/en/datasheet/stm32f407ve.pdf) |

На кристалле платы читается `STM32F407 VET6`; выбранная Marlin-плата также называется `BOARD_LOTMAXX_V1_7` для `STM32F407VET6`. Старое упоминание `VGT6` в ранних заметках было ошибочным: это другой вариант с другим объёмом Flash.

## Краткая справка

- Ядро Cortex-M4 с FPU и DSP-инструкциями, частота до 168 MHz, до 210 DMIPS.
- Три 12-битных ADC (до 2.4 MSPS каждый, до 24 внешних каналов), два 12-битных DAC.
- До 17 таймеров, включая `TIM1` и `TIM8` для motor-control/PWM. Лазерный PWM платы использует `TIM1_CH3` на `PE13`.
- Интерфейсы: 3x I2C, 4x USART и 2x UART, 3x SPI, 2x I2S, 2x CAN, SDIO, USB OTG FS/HS, Ethernet MAC, DCMI и FSMC.
- SWD/JTAG и трассировка доступны аппаратно. На плате подтверждён внешний кварц 25 MHz на `PH0`/`PH1`.

Официальная нумерация ниже взята из Figure 13 и таблиц 7/9 DS8626. Номер - именно физическая ножка корпуса при виде **сверху**, не позиция в разъёме платы.

## Как читать таблицу

- Все строки `GPIO` допускают обычный цифровой input/output и `EXTI<n>` по номеру ножки порта, если это не питание/специальный вывод. `EVENTOUT` не повторяется в каждой строке.
- В «Возможностях STM32» перечислены аппаратные alternate functions и аналоговые функции, доступные на этой физической ножке. Это возможности чипа, не одновременно включённые сигналы.
- В «Использовании Beta M» метки означают: **M** - задано в текущем Marlin `pins_LOTMAXX_V1_7.h`; **H** - подтверждено прозвонкой, измерением или работой узла; **S** - следует из подключённого штатного интерфейса/конфигурации, но трасса ещё не прозванивалась.
- Пустая ячейка в «Использовании Beta M» означает, что назначение платы не подтверждено. Это не означает свободный вывод: он может быть занят заводской прошивкой или скрытой дорожкой. Не подавать на него уровень до проверки схемы.

**Итог на текущем этапе:** из 100 физических ножек у **80** зафиксировано назначение Beta M либо системная функция питания/тактирования; у **20** назначение платы пока не установлено. Неподтверждённые номера: `1`, `15`, `23`, `24`, `59`, `67-69`, `82-85`, `88-90`, `93`, `95-98`.

На незапаянной колодке `WIFI` подтверждены только 3.3 V и GND. Её сигнальные дорожки пока не прозвонены до ножек MCU, поэтому незанятые ячейки в таблице ниже сохранены пустыми. См. [WIFI.md](WIFI.md).

## Полная таблица LQFP100

| № | Вывод STM32 | Использование Beta M V1.1 | Возможности STM32 |
|---:|---|---|---|
| 1 | `PE2` |  | GPIO/EXTI2; `TRACECLK`, `FSMC_A23`, `ETH_MII_TXD3` |
| 2 | `PE3` | **M/H** Laser Enable, active-low; идёт на лазерную часть, роль внутри SC-60 не доказана | GPIO/EXTI3; `TRACED0`, `FSMC_A19` |
| 3 | `PE4` | **M** E1 DIR | GPIO/EXTI4; `TRACED1`, `FSMC_A20`, `DCMI_D4` |
| 4 | `PE5` | **M** E1 STEP | GPIO/EXTI5; `TRACED2`, `FSMC_A21`, `TIM9_CH1`, `DCMI_D6` |
| 5 | `PE6` | **M** E1 ENABLE | GPIO/EXTI6; `TRACED3`, `FSMC_A22`, `TIM9_CH2`, `DCMI_D7` |
| 6 | `VBAT` | Резервное питание RTC; трасса на плате не проверена | Питание backup-domain/RTC, 1.8–3.6 V |
| 7 | `PC13` | **M** E0 DIR | GPIO/EXTI13; `RTC_OUT`, `RTC_TAMP1`, `RTC_TS`; low-speed I/O |
| 8 | `PC14/OSC32_IN` | **M** E0 STEP | GPIO/EXTI14; `OSC32_IN`; low-speed I/O |
| 9 | `PC15/OSC32_OUT` | **M** E0 ENABLE | GPIO/EXTI15; `OSC32_OUT`; low-speed I/O |
| 10 | `VSS` | Земля | Питание GND |
| 11 | `VDD` | Цифровое питание | Питание 1.8–3.6 V |
| 12 | `PH0/OSC_IN` | **H** вход кварца 25 MHz | GPIO/EXTI0; HSE `OSC_IN` |
| 13 | `PH1/OSC_OUT` | **H** выход кварца 25 MHz | GPIO/EXTI1; HSE `OSC_OUT` |
| 14 | `NRST` | Сброс; наличие SWD-площадки/трассы не подтверждено | Аппаратный reset, active-low |
| 15 | `PC0` |  | GPIO/EXTI0; `FSMC_SDNWE`; `ADC123_IN10` |
| 16 | `PC1` | **M** Z DIR | GPIO/EXTI1; `ETH_MDC`; `ADC123_IN11` |
| 17 | `PC2` | **M** Z STEP | GPIO/EXTI2; `SPI2_MISO`, `I2S2ext_SD`, `ETH_MII_TXD2`; `ADC123_IN12` |
| 18 | `PC3` | **M** Z ENABLE | GPIO/EXTI3; `SPI2_MOSI`, `I2S2_SD`; `ADC123_IN13` |
| 19 | `VDD` | Цифровое питание | Питание 1.8–3.6 V |
| 20 | `VSSA` | Аналоговая земля | Земля ADC/DAC |
| 21 | `VREF+` | Опорное напряжение ADC/DAC; трасса не проверена | Аналоговая опора ADC/DAC |
| 22 | `VDDA` | Аналоговое питание | Питание ADC/DAC 1.8–3.6 V |
| 23 | `PA0/WKUP` |  | GPIO/EXTI0, wakeup; `TIM2_CH1/ETR`, `TIM5_CH1`, `TIM8_ETR`, `USART2_CTS`, `ETH_MII_CRS`; `ADC123_IN0` |
| 24 | `PA1` |  | GPIO/EXTI1; `TIM2_CH2`, `TIM5_CH2`, `USART2_RTS`, `ETH_MII_RX_CLK`; `ADC123_IN1` |
| 25 | `PA2` | **M** Y DIR | GPIO/EXTI2; `TIM2_CH3`, `TIM5_CH3`, `TIM9_CH1`, `USART2_TX`, `ETH_MII_MDIO`; `ADC123_IN2` |
| 26 | `PA3` | **M** Y ENABLE | GPIO/EXTI3; `TIM2_CH4`, `TIM5_CH4`, `TIM9_CH2`, `USART2_RX`, `OTG_HS_ULPI_D0`, `ETH_MII_COL`; `ADC123_IN3` |
| 27 | `VSS` | Земля | Питание GND |
| 28 | `VDD` | Цифровое питание | Питание 1.8–3.6 V |
| 29 | `PA4` | **M** X DIR | GPIO/EXTI4; `SPI1_NSS`, `SPI3_NSS`, `USART2_CK`, `DCMI_HSYNC`, `OTG_HS_SOF`, `I2S3_WS`; `ADC12_IN4`, `DAC_OUT1` |
| 30 | `PA5` | **M** X STEP | GPIO/EXTI5; `SPI1_SCK`, `TIM2_CH1/ETR`, `TIM8_CH1N`, `OTG_HS_ULPI_CK`; `ADC12_IN5`, `DAC_OUT2` |
| 31 | `PA6` | **M** X ENABLE | GPIO/EXTI6; `SPI1_MISO`, `TIM1_BKIN`, `TIM3_CH1`, `TIM8_BKIN`, `TIM13_CH1`, `DCMI_PIXCLK`; `ADC12_IN6` |
| 32 | `PA7` | **M** `TEMP_0` / TH1 | GPIO/EXTI7; `SPI1_MOSI`, `TIM1_CH1N`, `TIM3_CH2`, `TIM8_CH1N`, `TIM14_CH1`, `ETH_MII_RX_DV/RMII_CRS_DV`; `ADC12_IN7` |
| 33 | `PC4` | **M** `TEMP_BED` / TB1 | GPIO/EXTI4; `ETH_MII_RXD0/RMII_RXD0`; `ADC12_IN14` |
| 34 | `PC5` | **M** `TEMP_1` / TH2 | GPIO/EXTI5; `ETH_MII_RXD1/RMII_RXD1`; `ADC12_IN15` |
| 35 | `PB0` | **M** Z probe input | GPIO/EXTI0; `TIM1_CH2N`, `TIM3_CH3`, `TIM8_CH2N`, `OTG_HS_ULPI_D1`, `ETH_MII_RXD2`; `ADC12_IN8` |
| 36 | `PB1` | **M** servo/BLTouch control | GPIO/EXTI1; `TIM1_CH3N`, `TIM3_CH4`, `TIM8_CH3N`, `OTG_HS_ULPI_D2`, `ETH_MII_RXD3`; `ADC12_IN9` |
| 37 | `PB2/BOOT1` | **M** board LED; boot strap must be preserved | GPIO/EXTI2; `BOOT1`, `EVENTOUT` |
| 38 | `PE7` | **M** Y STEP | GPIO/EXTI7; `FSMC_D4`, `TIM1_ETR` |
| 39 | `PE8` | **M/H** FAN0 / верхний MOSFET обдува модели | GPIO/EXTI8; `FSMC_D5`, `TIM1_CH1N` |
| 40 | `PE9` | **M/H** питание лазерного драйвера: MOSFET #2, +24 V | GPIO/EXTI9; `FSMC_D6`, `TIM1_CH1` |
| 41 | `PE10` | **M** нагрев стола | GPIO/EXTI10; `FSMC_D7`, `TIM1_CH2N` |
| 42 | `PE11` | **M** HEATER0 / хотэнд | GPIO/EXTI11; `FSMC_D8`, `TIM1_CH2` |
| 43 | `PE12` | **M/H** разрешение каскада лазера: MOSFET #5 | GPIO/EXTI12; `FSMC_D9`, `TIM1_CH3N` |
| 44 | `PE13` | **M/H** laser PWM, `TIM1_CH3`; на стоке около 1 kHz | GPIO/EXTI13; `FSMC_D10`, `TIM1_CH3` |
| 45 | `PE14` | **M/H** `74HC165 CLK` | GPIO/EXTI14; `FSMC_D11`, `TIM1_CH4` |
| 46 | `PE15` | **M/H** `74HC165 Q7` serial input | GPIO/EXTI15; `FSMC_D12`, `TIM1_BKIN` |
| 47 | `PB10` | **M** I2C EEPROM SDA, software I2C | GPIO/EXTI10; `I2C2_SCL`, `SPI2_SCK/I2S2_CK`, `USART3_TX` |
| 48 | `PB11` | **M** I2C EEPROM SCL, software I2C | GPIO/EXTI11; `I2C2_SDA`, `USART3_RX` |
| 49 | `VCAP_1` | Конденсатор внутреннего LDO | Выход внутреннего 1.2 V regulator; только внешний конденсатор |
| 50 | `VDD` | Цифровое питание | Питание 1.8–3.6 V |
| 51 | `PB12` | **M** SPI Flash CS | GPIO/EXTI12; `I2C2_SMBA`, `SPI2_NSS/I2S2_WS`, `USART3_CK`, `CAN2_RX`, `OTG_HS_ID` |
| 52 | `PB13` | **M** SPI Flash SCK | GPIO/EXTI13; `SPI2_SCK/I2S2_CK`, `TIM1_CH1N`, `USART3_CTS`, `CAN2_TX`, `OTG_HS_ULPI_D6` |
| 53 | `PB14` | **M** SPI Flash MISO | GPIO/EXTI14; `SPI2_MISO`, `TIM1_CH2N`, `TIM8_CH2N`, `USART3_RTS`, `TIM12_CH1`, `OTG_HS_ULPI_D5` |
| 54 | `PB15` | **M** SPI Flash MOSI | GPIO/EXTI15; `SPI2_MOSI/I2S2_SD`, `TIM1_CH3N`, `TIM8_CH3N`, `RTC_50Hz`, `TIM12_CH2`, `OTG_HS_ULPI_D4` |
| 55 | `PD8` | **M/H** `/PL` `74HC165`; не использовать как достоверный laser detect | GPIO/EXTI8; `FSMC_D13`, `USART3_TX` |
| 56 | `PD9` | **M/H** focus-концевик лазера, active-low | GPIO/EXTI9; `FSMC_D14`, `USART3_RX` |
| 57 | `PD10` | **M** `FIL_RUNOUT2` | GPIO/EXTI10; `FSMC_D15`, `USART3_CK` |
| 58 | `PD11` | **M** `FIL_RUNOUT` / MT_DET | GPIO/EXTI11; `FSMC_A16`, `USART3_CTS` |
| 59 | `PD12` |  | GPIO/EXTI12; `FSMC_A17`, `TIM4_CH1`, `USART3_RTS` |
| 60 | `PD13` | **M** Z stop | GPIO/EXTI13; `FSMC_A18`, `TIM4_CH2` |
| 61 | `PD14` | **M** Y stop; `HEATER_1` определён, но не используется при одном хотэнде | GPIO/EXTI14; `FSMC_D0/DA0`, `TIM4_CH3` |
| 62 | `PD15` | **M** X stop | GPIO/EXTI15; `FSMC_D1`, `TIM4_CH4` |
| 63 | `PC6` | **S** DWIN UART TX (`LCD_SERIAL_PORT 6`); требуется прозвонка до кабеля | GPIO/EXTI6; `TIM3_CH1`, `TIM8_CH1`, `I2S2_MCK`, `USART6_TX`, `DCMI_D0`, `SDIO_D6` |
| 64 | `PC7` | **S** DWIN UART RX (`LCD_SERIAL_PORT 6`); требуется прозвонка до кабеля | GPIO/EXTI7; `TIM3_CH2`, `TIM8_CH2`, `I2S3_MCK`, `USART6_RX`, `DCMI_D1`, `SDIO_D7` |
| 65 | `PC8` | **M** PSU / `PS_ON_PIN` | GPIO/EXTI8; `TIM3_CH3`, `TIM8_CH3`, `USART6_CK`, `DCMI_D2`, `SDIO_D0` |
| 66 | `PC9` | **M** power-loss input | GPIO/EXTI9; `I2C3_SDA`, `TIM3_CH4`, `I2S_CKIN`, `MCO2`, `DCMI_D3`, `SDIO_D1` |
| 67 | `PA8` |  | GPIO/EXTI8; `MCO1`, `TIM1_CH1`, `USART1_CK`, `OTG_FS_SOF`, `I2C3_SCL` |
| 68 | `PA9` |  | GPIO/EXTI9; `MCO2`, `TIM1_CH2`, `I2C3_SMBA`, `USART1_TX`, `DCMI_D0` |
| 69 | `PA10` |  | GPIO/EXTI10; `TIM1_CH3`, `USART1_RX`, `OTG_FS_ID` |
| 70 | `PA11` | **S** USB FS D- (принтер виден по USB) | GPIO/EXTI11; `TIM1_CH4`, `USART1_CTS`, `CAN1_RX`, `OTG_FS_DM` |
| 71 | `PA12` | **S** USB FS D+ (принтер виден по USB) | GPIO/EXTI12; `TIM1_ETR`, `USART1_RTS`, `CAN1_TX`, `OTG_FS_DP` |
| 72 | `PA13` | SWDIO/JTMS; трасса debug-разъёма не подтверждена | GPIO/EXTI13; `SWDIO`, `JTMS` |
| 73 | `VCAP_2` | Конденсатор внутреннего LDO | Выход внутреннего 1.2 V regulator; только внешний конденсатор |
| 74 | `VSS` | Земля | Питание GND |
| 75 | `VDD` | Цифровое питание | Питание 1.8–3.6 V |
| 76 | `PA14` | SWCLK/JTCK; трасса debug-разъёма не подтверждена | GPIO/EXTI14; `SWCLK`, `JTCK` |
| 77 | `PA15` | **M** SD card detect | GPIO/EXTI15; `JTDI`, `TIM2_CH1/ETR`, `SPI1_NSS/I2S1_WS` |
| 78 | `PC10` | **M** SD SCK | GPIO/EXTI10; `SPI3_SCK/I2S3_CK`, `USART3_TX`, `UART4_TX`, `SDIO_D2`, `DCMI_D8` |
| 79 | `PC11` | **M** SD MISO | GPIO/EXTI11; `I2S3ext_SD`, `USART3_RX`, `UART4_RX`, `SDIO_D3`, `DCMI_D4` |
| 80 | `PC12` | **M** SD MOSI | GPIO/EXTI12; `SPI3_MOSI/I2S3_SD`, `UART5_TX`, `SDIO_CK`, `DCMI_D9` |
| 81 | `PD0` | **M** SD CS / `SDSS` | GPIO/EXTI0; `CAN1_RX`, `FSMC_D2` |
| 82 | `PD1` |  | GPIO/EXTI1; `CAN1_TX`, `FSMC_D3` |
| 83 | `PD2` |  | GPIO/EXTI2; `TIM3_ETR`, `UART5_RX`, `SDIO_CMD`, `DCMI_D11` |
| 84 | `PD3` |  | GPIO/EXTI3; `FSMC_CLK`, `USART2_CTS` |
| 85 | `PD4` |  | GPIO/EXTI4; `FSMC_NOE`, `USART2_RTS` |
| 86 | `PD5` | **H** WIFI-1; свободен в текущем Marlin | GPIO/EXTI5; `FSMC_NWE`, `USART2_TX` |
| 87 | `PD6` | **H** WIFI-5; свободен в текущем Marlin | GPIO/EXTI6; `FSMC_NWAIT`, `USART2_RX` |
| 88 | `PD7` |  | GPIO/EXTI7; `FSMC_NE1/NCE2` |
| 89 | `PB3` |  | GPIO/EXTI3; `JTDO/TRACESWO`, `SPI1_SCK/I2S3_CK`, `TIM2_CH2` |
| 90 | `PB4` |  | GPIO/EXTI4; `NJTRST`, `SPI1_MISO`, `TIM3_CH1`, `I2S3ext_SD` |
| 91 | `PB5` | **M** DGUS/DWIN power pin, only when `DGUS_LCD_UI_LOTMAXX` enabled | GPIO/EXTI5; `I2C1_SMBA`, `SPI1_MOSI/I2S3_SD`, `CAN2_RX`, `OTG_HS_ULPI_D7`, `TIM3_CH2` |
| 92 | `PB6` | **H** WIFI-7; свободен в текущем Marlin | GPIO/EXTI6; `I2C1_SCL`, `TIM4_CH1`, `USART1_TX`, `CAN2_TX`, `FSMC_NL` |
| 93 | `PB7` |  | GPIO/EXTI7; `I2C1_SDA`, `TIM4_CH2`, `USART1_RX`, `TIM1_BKIN`, `FSMC_NL` |
| 94 | `BOOT0` | Boot strap; не использовать как GPIO | Выбор boot mode при reset |
| 95 | `PB8` |  | GPIO/EXTI8; `TIM4_CH3`, `I2C1_SCL`, `CAN1_RX`, `ETH_MII_TXD3`, `SDIO_D4` |
| 96 | `PB9` |  | GPIO/EXTI9; `TIM4_CH4`, `I2C1_SDA`, `SPI2_NSS/I2S2_WS`, `CAN1_TX`, `SDIO_D5` |
| 97 | `PE0` |  | GPIO/EXTI0; `TIM4_ETR`, `FSMC_NBL0`, `DCMI_D2` |
| 98 | `PE1` |  | GPIO/EXTI1; `FSMC_NBL1` |
| 99 | `VSS` | Земля | Питание GND |
| 100 | `VDD` | Цифровое питание | Питание 1.8–3.6 V |

## Выводы для дальнейшей разработки

1. Не считать `PE3` доказанным силовым включением лазера: его связь с лазерным жгутом установлена, а действие в SC-60 - нет. Для известного рабочего пути нужны `PE9`, `PE12` и PWM на `PE13`; детали - в [LASER.md](LASER.md).
2. `PD8` не использовать для автоопределения лазерной головы: это `/PL` `74HC165`, поэтому его уровень зависит от регистра и может быть высоким при отключённом модуле.
3. `PC6`/`PC7` и `PA11`/`PA12` обозначены как штатные DWIN/USB по периферийному назначению и программной конфигурации. Если нужно вмешательство в проводку, сначала подтвердить каждую линию прозвонкой.
4. Нельзя переиспользовать `PC13`–`PC15` как быстрые силовые сигналы: это low-speed I/O backup-domain. Нельзя использовать `VCAP_1`, `VCAP_2`, питание, землю, `BOOT0` и `NRST` как обычные GPIO.
