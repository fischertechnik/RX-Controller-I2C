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

### I2C Scan
```python
i2c = busio.I2C(board.SCL1, board.SDA1, frequency=400000)
while not i2c.try_lock():
    pass
try:
  print("I2C addresses found:", [hex(device_address) for device_address in i2c.scan()],)
finally:  # unlock the i2c bus
  i2c.unlock()
i2c.deinit()
```
### I2C Write
```python
with busio.I2C(board.SCL1, board.SDA1, frequency=400000) as i2c:
  device = I2CDevice(i2c, I2C_ADDRESS)
  with device:
    device.write(bytes([REG,VALUE]))
```

### I2C WriteRead
```python
with busio.I2C(board.SCL1, board.SDA1, frequency=400000) as i2c:
  device = I2CDevice(i2c, I2C_ADDRESS)
  with device:
    device.write(bytes([REG]))
    pbuf = bytearray(1)
    device.readinto(pbuf)
    VALUE = int(pbuf[0])
```

### Examples External I2C Modules
Some examples of external I2c modules already exist. These examples can be imported with the [ROBO Pro Coding](https://www.fischertechnik.de/en/apps-and-software#apps) app.

| sensor chip |  ROBO Pro Coding program name |
| ---         | --- |
|             | *test_RX_i2c_device_scan* |
| APDS9960    | *test_RX_i2c_device_apds9960* |

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

## Connector I2C - 10-pin  (Deprecated)
> [!WARNING]
> * Power supply for I2C is 3.3V
> * No separate power supply at the plug 

<img src="https://github.com/user-attachments/assets/2004514b-5a98-4904-ba74-0bb5a3d93dd0" width="400">
