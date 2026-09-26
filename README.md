# VOTOL Monitor V2

Android VOTOL monitor/configurator foundation.

## Implemented
- Android Bluetooth Classic RFCOMM/SPP using the standard SPP UUID.
- Paired-device picker.
- Raw HEX TX/RX logging.
- VOTOL SHOW live-data request.
- 24-byte live-data parser: voltage, signed current, RPM, controller/external temperature and state.
- VOTOL LDGET parameter-read request.
- Parsing of the documented 7 parameter blocks; Page 1/2/3 fields are displayed read-only.

## Protocol research
The repository includes docs/VOTOL_PROTOCOL.md and a capture workflow. Public protocol notes found in the VotolAIO project document the LDGET request and seven parameter blocks, including model, battery/voltage/current calibration, bus current, three-speed values, flux weakening, motor flags, pole pairs, EABS and throttle-related fields.

## Write safety
Parameter WRITE is NOT enabled yet. Public notes identify write-response markers, but they do not provide enough verified request/checksum semantics for a generic writer. V2 therefore remains read-only for parameters until real controller captures are compared and the write packet is independently verified.

## Build
Open in Android Studio and build the app module.
