
# PyHANbus

A Python implementation of the HAN (Home Area Network) protocol.


## Overview

This project begins as a fork of the well known PyModbus library. Its scope is to provide a working Modbus stack eclectic
enough to cover the customizations and extensions to the original protocol.

Historically the original PyModbus library provided some flexibility that would allow non-standard features like registers
longer than 16 bits to be supported. Later the authors considered not to be in their scope to support this capability 
and since have cut it off from their implementation.

This project aims to bring back the ability to handle registers of arbitrary length, and potentially other features that 
may be in violation of the original Modbus protocol, but which are de facto present in real world equipment.

## The EDP HAN bus

The particular case that motivated the introduction of the PyHANbus project was the Portuguese smart meter InovGrid scenario. When the utility company EDP started planning the transition to an all digital smart meter network,it decided that the communication between the equipment and the user client, as well as other equipment in the grid, had to follow strict protocol guidelines that they defined in this document:

https://www.e-redes.pt/sites/edd/files/normative_docs/DEF-C44-509.pdf

This specification takes the Modbus specification as the baseline, and complies with it regarding the structuring of the frames and all the definitions related to the communication, but extends it by allowing individual registers to have a variable length, instead of the fixed 16 bits of the original Modbus specification.

The EDP HAN specification does not define a specific limit to how many bytes a single register can have, but the ultimate constraint is the 256 bytes maximum length of the Modbus
frame.

### Structure of the request / response:

Request:

| Byte 0        | Byte 1                 | Byte 2                 | Byte 3                            | Byte 4                           |
|:--------------|:-----------------------|:-----------------------|:----------------------------------|:---------------------------------|
| Function code | Starting Address (MSB) | Starting Address (LSB) | Quantity of Input Registers (MSB) |Quantity of Input Registers (LSB) |

Response:

| Byte 0        | Byte 1         | Byte 2      | Byte 3          |   Byte 4    | ...       | Byte n      |
|:--------------|:---------------|:------------|:----------------|:------------|:----------|-------------|
| Function code | Byte Count (m) | Data byte m | Data byte m - 1 | ...         | ...       | Data byte 0 |

Constraints:

```
n < 256
m <= n - 2
m % 2 == 0
```

## References

 * [Original PyModbus documentation](PyModbus.rst)


## License Information

Released under the `BSD License <https://github.com/pymodbus-dev/pymodbus/blob/dev/LICENSE>`
