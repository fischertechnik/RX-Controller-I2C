>If you have any questions, please contact fischertechnik-technik@fischer.de

# RX Controller - I2C
The fischertechnik [RX controller](https://www.fischertechnik.de/en/toys/e-learning/rx-controller) has 2 connections labeled EXT1 and EXT2 (see 4 in the picture). Both connections are connected to the internal I2C bus.

<img src="https://github.com/user-attachments/assets/4d182355-48e9-44cd-be7e-8819102458d6" width="400">

> [!NOTE]
> [RX Controller Operating Instructions](https://www.fischertechnik.de/-/media/fischertechnik/rebrush/spielzeug/e-learning/smart-robots-max/lernmaterial/bedienunganleitung-rx/rx_controller_en.pdf)

## Connector I2C - Pin Assigment
![image](https://github.com/user-attachments/assets/9121a6af-6fb6-44de-b255-afbd045acae8)

> [!WARNING]
> Power supply for I2C is 3.3V

## Python Code
RX controller uses [CircuitPython](https://circuitpython.org/) which is based on Python. However, some commands differ considerably from Python. The basic functions for I2C are listed here.

### I2C Scan
```python
#https://learn.adafruit.com/circuitpython-essentials/circuitpython-i2c

import board
import busio

i2c =busio.I2C(board.SCL1, board.SDA1, frequency=400000)
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

### I2C Read
```python
with busio.I2C(board.SCL1, board.SDA1, frequency=400000) as i2c:
  device = I2CDevice(i2c, I2C_ADDRESS)
  with device:
    device.write(bytes([REG]))
    pbuf = bytearray(1)
    device.readinto(pbuf)
    VALUE = int(pbuf[0])
```

## Examples External I2C Modules

Some examples of external I2c modules already exist. These examples can be imported with the [ROBO Pro Coding](https://www.fischertechnik.de/en/apps-and-software#apps) app.

| sensor chip |  ROBO Pro Coding program name |
| --- | --- |
| all | **test_RX_i2c_device_scan** |
| APDS9960 | **test_RX_i2c_device_apds9960** |

## fischertechnik I2C Sensors

| Sensor                     | Item Number  | I2C Address | sensor chip |
| ---                        | ---          | ---         | --- |
|Environmental sensor 6-pin	 |182974     	  |0x76	        |BME680 |
|Environmental sensor 10-pin |167358	      |0x76	        |BME680 |
|RGB gesture sensor 6-pin	 |183267	      |0x39	        |APDS9960|
|RGB gesture sensor 10-pin   |186705	      |0x39	        |APDS9960|
|Kombisensor 6-pin           |201257	      |0x68	        |ICM42670 (GYRO + ACC)|
|                            |              |0x30 	      |MMC5603 (MAG)|
|Kombisensor 10-pin	         |158402	      |0x10	        |BMX055 (MAG)|
|                            |              |0x18       	|BMX055 (ACC)|
|                            |              |0x68	        |BMX055 (GYRO)|
|RGB color sensor	         |213965	      |0x14	        |Knobloch|
|NFC module	                 |-	            |0x24	        |PN532 NFC RFID Module|
|AGV charging module	     |-	            |0x26	        |Knobloch|
