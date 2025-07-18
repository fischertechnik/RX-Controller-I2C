>If you have any questions, please contact fischertechnik-technik@fischer.de

# RX Controller - I2C
The fischertechnik [RX controller](https://www.fischertechnik.de/en/toys/e-learning/rx-controller) has 2 connections labeled EXT1 and EXT2 (see 4 in the picture). Both connections are connected to the internal I2C bus, see [RX Controller Operating Instructions](https://www.fischertechnik.de/-/media/fischertechnik/rebrush/spielzeug/e-learning/smart-robots-max/lernmaterial/bedienunganleitung-rx/rx_controller_en.pdf).

| RX Controller | 6-pin Assigment |
| --- | --- |
|<img src="https://github.com/user-attachments/assets/4d182355-48e9-44cd-be7e-8819102458d6" width="400">|<img src="https://github.com/user-attachments/assets/9121a6af-6fb6-44de-b255-afbd045acae8" width="400">|

> [!WARNING]
> * Power supply for I2C is **3.3V**

## Python Code
RX controller uses [CircuitPython](https://circuitpython.org/) which is based on Python. However, some commands differ considerably from Python. The basic functions for I2C are listed [here](https://learn.adafruit.com/circuitpython-essentials/circuitpython-i2c).

> [!IMPORTANT]
> * To ensure that no other function uses the I2C bus, use ```i2c.trylock()``` and ```i2c.unlock()```
> * Use ```i2c.deinit()``` to turn off the I2C bus
> * If you use the ```with``` statement ([context manager](https://book.pythontips.com/en/latest/context_managers.html)), it automatically locks and unlocks the I2C interface

### I2C Imports
```python
import board
import busio
from adafruit_bus_device.i2c_device import I2CDevice
from fischertechnik.logging import log as print
```

![image](https://github.com/user-attachments/assets/a6ab6f5d-1f6a-430d-afac-cc1542a565d5)

### I2C Scan
```python
async def i2cScan():
    i2c = busio.I2C(board.SCL1, board.SDA1, frequency=400000)
    while not i2c.try_lock():
        pass
    try:
      #print("I2C addresses found:", [hex(device_address) for device_address in i2c.scan()],)
      i2cList = i2c.scan()
    finally:  # unlock the i2c bus
      i2c.unlock()
    i2c.deinit()
    return i2cList
```

![image](https://github.com/user-attachments/assets/4d2d1c66-8b26-4ef8-814f-9263d010d8ac)

### I2C Write Buffer
```python
async def i2cWriteBuffer(i2cAdr, write_buffer):
    with busio.I2C(board.SCL1, board.SDA1, frequency=400000) as i2c:
      with I2CDevice(i2c, i2cAdr) as device:
        device.write(bytes(write_buffer))
```

![image](https://github.com/user-attachments/assets/6cf32e8c-bd33-45dd-bb4c-2da75176b149)

### I2C Read Buffer
```python
async def i2cReadBuffer(i2cAdr, read_length):
    read_buffer = bytearray(read_length)
    with busio.I2C(board.SCL1, board.SDA1, frequency=400000) as i2c:
      with I2CDevice(i2c, i2cAdr) as device:
        device.readinto(read_buffer)
    return read_buffer
```

![image](https://github.com/user-attachments/assets/be48615e-73f8-413b-aa6f-d5763f89055d)

### I2C Write Read Buffer
```python
async def i2cWriteReadBuffer(i2cAdr, write_buffer, read_length):
    read_buffer = bytearray(read_length)
    with busio.I2C(board.SCL1, board.SDA1, frequency=400000) as i2c:
      with I2CDevice(i2c, i2cAdr) as device:
        device.write_then_readinto(bytes(write_buffer), read_buffer)
    return read_buffer
```

![image](https://github.com/user-attachments/assets/c4bed9b8-06e4-441d-9262-ec183a3117be)

### Examples External I2C Modules
Some examples of external I2c modules already exist. These examples can be imported with the [ROBO Pro Coding](https://www.fischertechnik.de/en/apps-and-software#apps) app.

<img src="https://github.com/user-attachments/assets/e2e3b200-94ff-4f32-8818-af6b87ce2d57" width="600">

| sensor chip |  ROBO Pro Coding program name |
| ---         | ---                           |
|             | *test_RX_i2c_device_scan*     |
|             | *test_RX_i2c_device_template* |
| APDS9960    | *test_RX_i2c_device_apds9960* |

[Further examples](https://elssner.github.io/ft-Controller-I2C/) of [I2C modules](https://elssner.github.io/ft-Controller-I2C/#tabelle-1) can be found in a ROBO Pro Coding project called **ft_Controller_I2C**. The documentation (German) was created by the user [elssner](https://github.com/elssner). Many thanks for the very detailed examples!

### Using I2C with Blockly and Python

#### RX firmware 0.23.1
Please note that with the RX firmware 0.23.1 it is not possible to access from Blockly and Python at the same time, as the I2C instance cannot be used simultaneously.

If the following error occurs, the RX controller must be switched off and on again.
```python
Traceback (most recent call last):
  File "asyncio/core.py", line 246, in run_until_complete
  File "user/mainCode.py", line 100, in run
ValueError: SCL1 in use
```

#### RX firmware 0.27.0
In RX firmware 0.27.0 it is now possible to access from Blockly and Python at the same time. Use the following function to get the I2CDevice:

```python
global i2c
import fischertechnik.factories as rx_factory
if rx_factory.i2c_factory is None:
  rx_factory.init_i2c_factory()
i2c = rx_factory.i2c_factory.get_i2c_bus(instance)._i2c
```

## fischertechnik I2C Sensors
Some I2C addresses are already reserved, see e.g. [here](https://i2cdevices.org/addresses) or [www.i2c-bus.org](https://www.i2c-bus.org/). All fischertechnik I2C sensors are listed in the table below.

| Sensor                     | Item Number  | I2C Address | sensor chip          | RX Controller           | TXT 4.0 Controller      | TXT Controller |
| ---                        | ---          | ---         | ---                  | ---                     | ---                     | ---            |
|Environmental sensor 6-pin	 |182974     	|0x76	      |BME680                | Yes                     | Yes                     |                |
|Environmental sensor 10-pin |167358	    |0x76	      |BME680                | Yes, I2C-Adapter 186150 | Yes, I2C-Adapter 186150 | Yes (C/C++)    |
|RGB gesture sensor 6-pin	 |183267	    |0x39	      |APDS9960              | Yes                     | Yes                     |                |
|RGB gesture sensor 10-pin   |186705	    |0x39	      |APDS9960              | Yes, I2C-Adapter 186150 | Yes, I2C-Adapter 186150 | Yes            |
|Kombisensor 6-pin           |201257	    |0x68	      |ICM42670 (GYRO + ACC) | Yes (FW>=0.26.2)        | Yes                     |                |
|                            |              |0x30 	      |MMC5603 (MAG)         | ...                     | ...                     |                |
|Kombisensor 10-pin	         |158402	    |0x10	      |BMX055 (MAG)          | Yes, I2C-Adapter 186150 | Yes                     |                |
|                            |              |0x18         |BMX055 (ACC)          | ...                     | ...                     |                |
|                            |              |0x68	      |BMX055 (GYRO)         | ...                     | ...                     |                |
|RGB color sensor	         |213965	    |0x14	      |Knobloch              |                         | Yes (txtapi>=6.4.0)     |                |
|NFC module	                 |-	            |0x24	      |PN532 NFC RFID Module |                         | Yes                     | Yes (C/C++)    |
|AGV charging module	     |-	            |0x26	      |Knobloch              |                         | Yes                     |                |
|RESERVED                    |              |0x8          |                      | RESERVED                |                         |                |

## Connector I2C - 10-pin (TXT Controller, Deprecated)
> [!WARNING]
> * Power supply for I2C is 3.3V
> * No separate power supply at the plug 

<img src="https://github.com/user-attachments/assets/2004514b-5a98-4904-ba74-0bb5a3d93dd0" width="400">
