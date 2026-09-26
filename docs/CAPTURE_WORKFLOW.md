# Parameter protocol capture workflow

1. Connect the controller using the same physical adapter used by the official VOTOL software.
2. Start V2 in **Capture Only** mode.
3. Record raw TX/RX bytes while the official software connects.
4. Change exactly one parameter by a small amount.
5. Save the TX packet and the controller response.
6. Restore the original value and capture again.
7. Compare the two TX packets byte-by-byte.
8. Identify value bytes, page/parameter selector bytes, packet length, XOR/checksum and terminator.
9. Repeat for Page 1, Page 2 and Page 3.
10. Only after several independent captures agree should a READ/WRITE implementation be enabled.

V2 should store captures as timestamped JSON/HEX so they can be compared later without writing anything to the controller.
