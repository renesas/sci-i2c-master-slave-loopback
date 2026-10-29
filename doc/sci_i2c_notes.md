# SCI I2C Master Loopback

## Overview

This software building block (SBB) demonstrates SCI I2C Master operation in a hardware loopback configuration with an IIC Slave driver on supported Renesas RA FPB boards.

After successful initialization, the reference application continuously transmits and receives 6 bytes of data. It compares the received data with the transmitted data:

- If the data matches, the on-board LEDs blink.
- If the data does not match, the LEDs remain ON.
- Status and failure messages are displayed on a serial terminal.

## Supported Boards

This document covers only the following boards:

- FPB-RA2E3
- FPB-RA4E2
- FPB-RA6E2
- FPB-RA8E1

> **Important:** The source `readme.txt` does not provide pin, channel, jumper, or validation information for the FPB-RA0L1. Board-specific settings for the FPB-RA0L1 must be defined and verified before running this SBB on that board.

## What Use Case Does This SBB Demonstrate?

The SBB demonstrates how to:

1. Configure an SCI peripheral for I2C Master operation.
2. Configure an IIC peripheral for I2C Slave operation.
3. Connect the Master and Slave interfaces through external jumper wires.
4. Transfer a 6-byte data sequence continuously.
5. Compare transmitted and received data.
6. Report pass or fail status through LEDs and serial-terminal messages.

The loopback arrangement is intended as a functional reference for SCI I2C Master communication, callback-driven transfer handling, board-level I2C wiring, and basic data-integrity verification.

## What Does the SBB Core Code Do?

The reusable portion of the SBB is expected to coordinate the following operations:

- Initialize the SCI I2C Master interface.
- Initialize the IIC Slave interface.
- Start the Master-to-Slave transfer.
- Receive the data through the Slave interface.
- Track transfer completion and error events.
- Compare the transmitted and received 6-byte buffers.
- Return or expose transfer status to the reference application.

The source `readme.txt` describes the behavior but does not identify the source filenames, reusable module boundaries, callback names, or public API names. Those interfaces should be documented from the implementation before publishing the SBB.

## Reusable SBB Code and Reference Application Code

### Reusable SBB code

Code is reusable when it implements the SCI I2C Master and IIC Slave initialization, transfer, callback, synchronization, and data-verification behavior without depending on a specific board's LEDs, connector names, or terminal presentation.

### Reference application code

The following behavior is reference-application or demonstration behavior:

- Continuous execution of the 6-byte loopback test.
- Blinking the on-board LEDs when the comparison succeeds.
- Keeping the LEDs ON when the comparison fails.
- Printing status and failure messages to a serial terminal.
- Applying board-specific channels, pins, connector positions, and jumper settings.

Applications adopting the SBB may replace these demonstration behaviors while retaining the reusable transfer logic.

## Public Functions to Review First

The source `readme.txt` does not list public function names. Before integration, identify and document the implementation functions responsible for:

1. SBB initialization.
2. Starting a transfer.
3. Handling SCI I2C Master events.
4. Handling IIC Slave events.
5. Checking transfer completion.
6. Reporting transfer or comparison status.
7. Closing or resetting the SBB, if supported.

Do not assign API names based only on this document. Use the names exported by the SBB source and header files.

## Hardware Requirements

- One supported Renesas RA FPB board.
- One USB cable suitable for programming and debugging the selected board.
- Two jumper wires for the SDA and SCL loopback connections.
- A host PC with a serial-terminal application, such as Tera Term, when terminal logs are required.

## Board Configuration and Connections

### FPB-RA2E3

Close E25, E28, E46, and E47 to use the J-Link OB VCOM interface for the serial terminal.

Both the SCI I2C Master and IIC Slave use channel 0.

| Signal | SCI I2C Master | Connection | IIC Slave |
|---|---|---|---|
| SDA | SDA0, P101, J4:11 | Jumper wire | SDA0, P401, J3:2 |
| SCL | SCL0, P100, J4:12 | Jumper wire | SCL0, P400, J3:1 |

### FPB-RA4E2

The SCI I2C Master uses channel 9. The IIC Slave uses channel 0.

| Signal | SCI I2C Master | Connection | IIC Slave |
|---|---|---|---|
| SDA | SDA9, P109, J4:2 | Jumper wire | SDA, P101, J4:15 |
| SCL | SCL9, P110, J4:3 | Jumper wire | SCL, P100, J4:16 |

### FPB-RA6E2

The SCI I2C Master uses channel 9. The IIC Slave uses channel 0.

| Signal | SCI I2C Master | Connection | IIC Slave |
|---|---|---|---|
| SDA | SDA9, P109, J4:2 | Jumper wire | SDA, P101, J4:15 |
| SCL | SCL9, P110, J4:3 | Jumper wire | SCL, P100, J4:16 |

### FPB-RA8E1

The SCI I2C Master uses channel 0. The IIC Slave uses channel 1.

| Signal | SCI I2C Master | Connection | IIC Slave |
|---|---|---|---|
| SDA | SDA0, P113, J3:17 | Jumper wire | SDA1, P512, J4:3 |
| SCL | SCL0, P112, J3:18 | Jumper wire | SCL1, P511, J4:4 |


## Electrical Assumptions

For the documented FPB-RA2E3, FPB-RA4E2, FPB-RA6E2, and FPB-RA8E1 configurations:

- The original project notes do not require external I2C pull-up resistors for these boards.
- SDA and SCL must be configured as N-channel open-drain outputs.

Verify the board schematic and populated components before reusing these assumptions on a modified board or another hardware design.

## Serial Terminal Configuration

Use the serial port provided by the J-Link On-Board interface where available.

| Setting | Value |
|---|---|
| Speed | 115200 baud |
| Data | 8 bits |
| Parity | None |
| Stop bits | 1 |
| Flow control | None |

For Tera Term:

1. Open **Setup > Terminal**.
2. Under **New-line**, set both **Receive** and **Transmit** to **CR**.
3. Enable **Local echo** if typed characters should be echoed.

## What Must Change for Another Board or Application?

Review and update all hardware-dependent configuration before integration.

### Pins and peripheral channels

- Select SCI and IIC channels supported by the target MCU.
- Assign valid SDA and SCL pins through the FSP configuration.
- Update connector wiring and loopback instructions.
- Configure SDA and SCL as N-channel open drain.
- Confirm that the bus has suitable pull-up resistors.

### Clocks and timing

- Recalculate I2C timing for the target peripheral clock.
- Confirm that the selected bus rate is supported by both interfaces.
- Review timeout values and synchronization assumptions in the implementation.

The source `readme.txt` does not state the configured I2C bus rate, peripheral clock, timeout values, or measured timing margins.

### LEDs and terminal output

- Replace the reference LED mapping with resources available on the target board.
- Update the UART or VCOM configuration used for terminal messages.
- Remove LED or terminal dependencies if they are not required by the product application.

### Memory, task priorities, and middleware

The source `readme.txt` does not specify memory usage, RTOS requirements, task priorities, stack sizes, or middleware configuration. If the SBB is integrated into an RTOS application, review callback context, synchronization, task priority, stack usage, and shared-resource access in the implementation.

## Dependencies

The demonstrated project depends on:

- Renesas Flexible Software Package (FSP) configuration for SCI I2C Master operation.
- An IIC Slave driver instance.
- MCU pin configuration for SCI/IIC SDA and SCL signals.
- Open-drain I2C outputs and board-level pull-ups.
- Two jumper wires for the physical loopback.
- Board LED resources for pass/fail indication.
- A serial output path and terminal application for status messages.

The source `readme.txt` does not provide FSP version requirements, exact driver module names, middleware versions, or resource-instance symbols. Record those details from the project configuration and generated files for each released SBB version.

## Demonstrated Behavior and Performance

The documented reference behavior is:

- Six bytes are transmitted and received continuously after successful initialization.
- Matching data causes the on-board LEDs to blink.
- Mismatched data causes the LEDs to remain ON.
- Failure messages and status are displayed on the terminal.

The source `readme.txt` does not provide throughput, latency, CPU usage, memory usage, bus frequency, test duration, transfer count, error rate, or other measured performance results. Do not treat the functional loopback result as a quantified performance benchmark.

## File Ownership and Modification Guidance

The source `readme.txt` does not list the project files. Classify files in the delivered project before modification using the following rules.

### Safe places for customer changes

- Customer application source files.
- Board-specific configuration owned by the customer.
- SBB wrapper or adaptation layers.
- Clearly identified user-editable regions in generated integration files.

### Reusable SBB files

Keep reusable transfer logic isolated from board-specific LED, terminal, and pin configuration. Local changes to reusable SBB files should be tracked so they can be reviewed during an upgrade.

### Generated or preset files

Treat FSP-generated source, pin configuration, and configuration artifacts as generated unless the project explicitly documents them as hand-maintained. Make configuration changes through the supported configuration source rather than editing generated output directly.

### Demo-only files

Board LED control, loopback test sequencing, and terminal presentation should be treated as demo-only unless the product application explicitly adopts them.

## Updating to a Newer SBB Release After Local Modification

To reduce upgrade conflicts:

1. Keep the released SBB source unchanged where possible.
2. Place product-specific behavior in wrapper, adaptation, or application files.
3. Keep board configuration separate from reusable transfer logic.
4. Record the adopted SBB version and all local modifications.
5. When adopting a newer release, compare the old release, locally modified version, and new release.
6. Reapply only the required local changes.
7. Regenerate FSP-managed files using the FSP version required by the new release.
8. Rebuild and repeat the loopback, LED, terminal, and error-path tests on every supported target board.

Direct edits to reusable or generated files increase the effort required to adopt a newer release.

## Expected Result

After the board is configured, wired, programmed, and started:

1. The SCI I2C Master transmits a 6-byte data sequence.
2. The IIC Slave receives the sequence through the external SDA and SCL loopback wiring.
3. The application compares the transmitted and received data.
4. The LEDs blink while the data matches.
5. The LEDs remain ON if a mismatch is detected.
6. Status and failure information is displayed on the serial terminal.

