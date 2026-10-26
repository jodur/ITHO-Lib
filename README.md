# ITHO-Lib

An Arduino-style C++ library for controlling Itho ventilation units (e.g. CVE / HRU fans) using a
CC1101 sub-GHz transceiver. It can send the commands of an Itho remote control (standby, low,
medium, high, full, timers, join, leave) and receive/parse packets sent by original remotes.

## Contents

| File | Purpose |
| --- | --- |
| `CC1101.h/.cpp` | Low-level CC1101 radio driver over SPI |
| `CC1101Packet.h` | Raw CC1101 packet container |
| `IthoCC1101.h/.cpp` | Itho protocol layer: send/receive commands, join/leave |
| `IthoPacket.h` | Itho packet and `IthoCommand` definitions |

## Prerequisites

- An Arduino-compatible board (e.g. ESP8266/ESP32, AVR) and the Arduino core (`Arduino.h`, `SPI.h`).
- A CC1101 module wired to the board's SPI bus (plus CS and GDO pins, see `CC1101.h` for pin definitions).
- Check that the radio frequency of your module (868 MHz in Europe) matches your Itho unit.

## Installation

Copy this repository into your Arduino `libraries/` folder (e.g. `libraries/ITHO-Lib`) or add the
files to your PlatformIO/Arduino project, then `#include "IthoCC1101.h"`.

## Usage

```cpp
#include <SPI.h>
#include "IthoCC1101.h"

IthoCC1101 rf;

void setup() {
  Serial.begin(115200);
  rf.init();          // reset and configure the CC1101
  rf.initReceive();   // start listening for remote packets
}

void loop() {
  if (rf.checkForNewPacket()) {
    IthoCommand cmd = rf.getLastCommand();
    Serial.println(rf.getLastIDstr());
    if (cmd == IthoHigh) {
      Serial.println("Remote pressed HIGH");
    }
  }
}

// To transmit a command (after the unit has been joined):
//   rf.sendCommand(IthoLow);
//   rf.initReceive();   // return to receive mode
```

`IthoCC1101(counter, sendTries)` lets you set the initial message counter and the number of times
a command is repeated per send.

## Contributing

Contributions are welcome. Please open an issue to discuss larger changes, keep pull requests
focused, avoid breaking the public API, and describe how you tested on hardware.
