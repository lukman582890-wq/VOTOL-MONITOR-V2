# VOTOL Monitor V2

Android/Web hybrid VOTOL monitor and configurator.

## Transport
- USB Serial 115200
- Web Bluetooth BLE
- Android Bluetooth Classic RFCOMM/SPP

## Status
Transport bridge is scaffolded. VOTOL parameter READ/WRITE remains gated on verified packet layout and checksum from controller captures or a known-good implementation.

## Safety
Do not write unknown packets to a controller. Verify protocol and checksum against real traffic before enabling parameter writes.
