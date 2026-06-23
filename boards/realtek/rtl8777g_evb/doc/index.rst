.. zephyr:board:: rtl8777g_evb

Overview
********

The RTL8777G Dongle Evaluation Board provides a hardware environment for user development, including:

- 1x Reset button
- 4x Buttons
- 3x LEDs
- 1x Micro USB-B port (connected to FT232RL USB-to-UART chip)
- 1x USB-A connector
- 25x Exposed pins (user-configurable)
- On-board ceramic antenna and IPEX socket (for optional external antenna)
- 5V to 3.3V LDO
- UART programming interface

The RTL8777G Dongle Evaluation Board features the RTL8777GKF-VA5 SoC.
RTL8777G is an ultra-low power wireless microcontroller that supports Bluetooth 5.3/Zigbee/Thread/Matter protocols.

.. note::
    The RTL8777G is part of the RTL87x2G series chip family (despite the naming inconsistency).

Hardware
********

SoC Series
==========

The RTL8777G/RTL87x2G series comprises various chip types, each supporting different hardware features.

Below are the common hardware features:

- Realtek KM4 core compatible with Arm Cortex-M55, running at 40MHz (Maximum 125MHz)
- M-profile Vector Extension (MVE) for vector computation
- 32KB I-Cache, 16KB D-Cache, and 384KB SRAM
- Hardware Keyscan / Quad Decode
- Flash On-The-Fly Decryption
- Embedded IR TX/RX
- ISO7816 Interface
- SPIC/SPI_m/SPI_s/SDIO/SD (eMMC)
- Low Power Comparator
- 8-Channel AUXADC
- 24-bit HD ADC
- CAN Bus
- RMII for Ethernet
- I2S/DAC/AMIC/DMIC/PDM
- SPIC/RGB888/SEGCOM Display Interfaces
- USB 2.0 High-Speed Interface

For detailed hardware information regarding specific part numbers of the RTL87x2G series, please refer to the `RTL87x2G Introduction`_.

Supported Features
==================

.. zephyr:board-supported-hw::

Connections and IOs
===================

Refer to the Realtek `RTL8777G Dongle Evaluation Board Guide`_ for detailed information about the board interfaces.

System Clock
============

The RTL8777G series SoC is configured to use the internal 32KHz clock as a source for the system clock.

Serial Port
===========

The RTL8777G series SoC has 6 UARTs. By default, UART2 is configured for the console and log output.

Flashing
========

Before flashing, please ensure the flashing runner :ref:`runner_mpcli` is installed. This is the default flashing runner for the RTL87x2G series SoCs.
As an (open-source) alternative, the Python-based west runner :ref:`runner_rtkprog` can be used, that supports auto-flashing.

**Flashing Setup**
The board is flashed through the serial interface provided by the on-board FT232RL USB-to-UART converter.
- **USB Connection**: Connect a USB cable to the **Micro USB-B** port located on the left of the EVB.
- **Jumper Configuration**: Use jumpers to connect **P3_1** to TX and **P3_0** to RX to utilize the on-board FT232RL USB-to-UART converter.

**Auto Flashing**
The :ref:`runner_rtkprog` runner supports flashing and running the program without manual re-wiring.
- Use a jumper wire to connect the **DTR** pin to the **LOG** pin (P0_3).
- Solder a jumper wire from the **RTS** test point (on the bottom of the EVB) and connect it to the **RST** (reset) pin.

**Manual Flashing**
Manually enter **Download Mode** by performing the following steps:
- Use a jumper to connect the **LOG** pin (P0_3) with **GND** (ground).
- Press the **RST** (reset) button once to reboot the SoC and enter download mode.
After flashing, enter the **Normal Mode** again, by removing the jumper and pressing **RST** (reset).

Flashing Realtek Images
==========================

To successfully run Zephyr on the RTL87x2G board, six essential images provided by Realtek must be programmed into the board, in addition to the Zephyr image.

To fetch these essential images, run the following command:

.. code-block:: console

   west blobs fetch hal_realtek --allow-regex 'bee/rtl87x2g/.*'

After entering download mode, run the following command from the directory containing a ``build`` folder:

.. code-block:: console

   west flash --port <port_name> --mp-json <zephyr workspace>/modules/hal/realtek/bee/tools/mpcli/configs/rtl87x2g_essential_images.json

.. note::
   - The "west flash" command assumes you are running it from the root where the ``build`` directory resides. If not, you must specify the build directory using the ``--build-dir`` or ``-d`` option.
   - Before flashing, ensure the serial port is available and not occupied by other applications (such as a serial console).

Flashing Zephyr
===============

Follow the steps below to build and flash the :zephyr:code-sample:`hello_world` application.

   .. zephyr-app-commands::
      :zephyr-app: samples/hello_world
      :board: rtl8777g_evb
      :goals: build flash
      :flash-args: --port <port_name>

After flashing, run the application by entering **Normal Mode** again (see **Manual Flashing**), or when using **Auto Flashing**, use this command to open a serial terminal:
``rtkprog --port <port_name> terminal``

When observing the terminal, after pressing the reset button on the EVB, you should see the output ``Hello World! rtl8777g_evb`` in your terminal.


References
**********

.. target-notes::

.. _RTL87x2G Introduction:
    https://www.realmcu.com/en/Home/Product/RTL8762G-RTL877xG-Series

.. _RTL8777G Dongle Evaluation Board Guide:
    https://docs.realmcu.com/matter/en/latest/doc/evb_guide/text_en/rtl8777g_dongle.html
