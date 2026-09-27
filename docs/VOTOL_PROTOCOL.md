# VOTOL EM Serial Protocol — verified reference set

Sources reviewed: community captures and open-source implementations found on GitHub.

## Transport
- Serial baud is interface/version dependent in public sources: 9600 and 115200 are both reported. The Android app currently uses the Bluetooth module transparently and therefore cannot change the module/controller UART baud from the app. If TX is visible but RX is empty, baud mismatch is a primary hardware-side suspect.
- Controller response is a 24-byte frame beginning `C0 14 0D 59 42`.
- Master request is a 24-byte frame beginning `C9 14 02 53 48 4F 57` for `SHOW`.

## SHOW request
Known frame examples:

`C9 14 02 53 48 4F 57 00 00 00 00 00 AA 00 00 00 1E AA 04 67 00 F3 52 0D`

The frame uses byte 22 as XOR of bytes 0..21 and byte 23 as `0D`.

Byte 12: `AA` local mode, `55` remote mode.

## Live response
Example:

`C0 14 0D 59 42 02 14 00 0F 01 00 00 00 00 02 B8 5D 4B 22 D6 80 03 01 0D`

Fields:
- B0..B4: response header
- B5..B6: battery voltage, value / 10
- B7..B8: signed battery current, value / 10
- B10..B13: 32-bit fault code
- B14..B15: RPM, unsigned integer
- B16: controller temperature = raw - 50 C
- B17: external/motor temperature = raw - 50 C
- B18..B19: temperature coefficient / auxiliary value
- B20: gear and status bits
- B21: controller status: 0 idle, 1 init, 2 start, 3 run, 4 stop, 5 brake, 6 wait, 7 fault
- B22: XOR of B0..B21
- B23: packet terminator `0D`

B20 bits:
- bits 0..1: gear 0=L, 1=M, 2=H, 3=S
- bit 2: reverse
- bit 3: park
- bit 4: brake
- bit 5: anti-theft
- bit 6: side stand
- bit 7: regen


## Parameter READ response framing
A public capture shows `LDGET` returning seven 24-byte frames with the form:

`C0 14 05 52 [page] [17-byte payload] [XOR] 0D`.

For each frame, the application converts it to an 18-byte logical block:
- logical byte 0 = page number (1..7)
- logical bytes 1..17 = payload bytes B5..B21
- physical B22 = XOR of physical B0..B21
- physical B23 = `0D`

This framing is important: treating the raw 24-byte parameter response as an 18-byte block causes the page fields to be parsed incorrectly.

## Parameter READ/WRITE status
A complete, controller-version-independent parameter READ/WRITE map was **not** found in the reviewed sources. Public captures do establish the 24-byte LDGET response framing above, but field meanings and WRITE behavior can vary by controller generation. The app therefore treats the READ framing as a protocol implementation detail, while WRITE remains experimental and must be verified against the user's actual EM-50 before relying on it.

Therefore V2 must not invent parameter packets. It should first capture traffic from the official VOTOL PC software while reading/writing one parameter at a time, then derive field offsets, packet type, length, and checksum.

## Safety gate
Do not enable arbitrary parameter writes until a packet has been captured from a real controller and replay-tested with checksum validation. The application should keep a READ/capture-first workflow. WRITE is exposed only behind an explicit confirmation and must not be considered hardware-verified.
