
atsha204-i2c
=======

<a href="https://scan.coverity.com/projects/3415">
  <img alt="Coverity Scan Build Status"
       src="https://scan.coverity.com/projects/3415/badge.svg"/>
</a>[![Stories in Ready](https://badge.waffle.io/cryptotronix/atsha204-i2c.png?label=ready&title=Ready)](https://waffle.io/cryptotronix/atsha204-i2c)


Kernel module for Atmel's ATSHA204 I2C device.

This software is in ***ALPHA***. It is subject to drastic refactoring
at will by the author.

This module provides the following features:

- An I2C driver [working]
- Sysfs for serial number [working]
- Char device for random data [working]


SYSFS
----

The systfs looks like this:

```
|-- configlocked
|-- configzone
|-- datalocked
|-- driver -> ../../../../../bus/i2c/drivers/atsha204-i2c
|-- misc
|   `-- atsha0
|       |-- dev
|       |-- device -> ../../../1-0060
|       |-- power
|       |   |-- autosuspend_delay_ms
|       |   |-- control
|       |   |-- runtime_active_time
|       |   |-- runtime_status
|       |   `-- runtime_suspended_time
|       |-- subsystem -> ../../../../../../../class/misc
|       `-- uevent
|-- modalias
|-- name
|-- power
|   |-- autosuspend_delay_ms
|   |-- control
|   |-- runtime_active_time
|   |-- runtime_status
|   `-- runtime_suspended_time
|-- serialnum
|-- subsystem -> ../../../../../bus/i2c
`-- uevent
```

The unique features are: configlocked, configzone, datalocked, and
serialnum.

configlocked & datalocked return a 1 or 0 where 1 indicates that zone
is "locked".

serial number returns the chip's unique serial number.

configzone dumps the chip's entire configuration zone.

RANDOM
-----

This driver plugs into /dev/hwrng. See the /dev/hwrng [documentation](https://www.kernel.org/doc/Documentation/hw_random.txt)
for how to use / switch random number generators.

/dev/atshaX
------

The driver handles the communication layer. It expects commands in the
format:

```
[Opcode (1)][Param1 (1)][Param2 (2)][[Data (x)]]
```

The number indicate the number of bytes. Data is optional. The data
must be written to the fd in one shot. The driver will pre-pend the
length and append the crc.

The driver will perform a write AND a read as there are specific
timing constraints when the data must be read. The read data is cached
until the user reads the data. The user receives the message ONLY, the
single byte size and crc are removed.
