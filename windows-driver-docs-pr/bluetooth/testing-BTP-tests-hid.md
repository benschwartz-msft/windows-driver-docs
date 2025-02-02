---
title: Microsoft Bluetooth Test Platform - HID
description: Bluetooth Test Platform (BTP) HID tests.
ms.date: 05/05/2022
---

# BTP HID Tests

The BTP HID tests verify the ability of the local system to pair with a remote radio over BR/EDR or LE and validate HID functionality.

## Setting Up

First check that the green power indicator, an optional yellow test LED, and 3 orange LEDs on the Traduci are on. Confirm that the SUT's Bluetooth radio is powered on and that the appropriate device(s) are correctly plugged in to the Traduci. Currently the RN42 device can **only** be plugged into JB. Similarly, the Bluefruit device can **only** be plugged into JC. More detailed information on setting up can be found at [Setting up BTP](testing-BTP-setup.md).

Information and purchasing information for supported devices can be found [Supported BTP Hardware](testing-BTP-hw.md).

## Supported devices

- [RN42](testing-BTP-hw-rn42.md)
- [Bluefruit Friend](testing-BTP-hw-bluefruit-Friend.md)
- [Bluefruit Feather](testing-BTP-hw-bluefruit-Feather.md)

## Running the HID Tests

Navigate to the folder where the BTP package was extracted. It will typically be under `C:\BTP`. In a folder named after the version of the package, you will find the scripts referenced below. Then run either:

- `RunHidTests.bat <device name>` from an elevated command prompt or
- `RunHidTests.ps1 <device name>` from an elevated PowerShell console

Information on available device name parameters can be found [Bluetooth Test Platform supported hardware](testing-BTP-hw.md)

You can also include the optional parameter `-VerboseLogs` at the end to get a more verbose output of inner operations of BTP.

As a test starts the red LED next to the 12-pin adapter will turn on once the command from the test to power the Pmod device has been sent. This LED will be turned off at the end of every test. If it is on at the start of the next test due the previous test failing, we will attempt to power it down and power it back on to return it to a known state. If the power cycle fails, the test will fail due to the Pmod device being in an unknown state.

## Capturing Logs

To capture the Bluetooth logs, follow the instructions at [The Bus tools for Windows Repo on GitHub](https://github.com/microsoft/busiotools/blob/master/bluetooth/tracing/readme.md).

To parse the Bluetooth logs, follow the instructions for the [BTETLParse tool](testing-BTP-tools-btetlparse.md).

## Known issues

- Stress tests: Tests run in a tight loop using an LE device may cause pairing or unpairing to fail.
- When running these tests with an LE HID device, they may infrequently fail due to validation intended to catch unexpected disconnections. Sometimes these disconnections get automatically recovered (failures to establish), but the test still fails the validation. This can happen more frequently in noisy RF environments.
