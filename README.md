# bme280-sensor

Welcome to bme280-sensor, a Node.js I2C driver for the BME280 Humidity, Barometric Pressure, Temperature Sensor. 

This module uses [i2cb-bus](https://github.com/fivdi/i2c-bus) which should provide access with Node.js on Linux boards like the Raspberry Pi Zero, 1, 2, or 3, BeagleBone, BeagleBone Black, or Intel Edison.

Note that while the BME280 device does report temperature, it is measured by the internal temperature sensor. This temperature value depends on the PCB temperature and sensor element self-heating. Therefore ambient temperature is typically reported above actual ambient temperature.

This Node project needs to talk directly to the I2C bus requires access to /dev/i2c, so you will typically need run Node with elevated privileges.

## Example Code

```
const BME280 = require('bme280-sensor');
const bme280 = new BME280();

// The BME280 constuctor options are optional. Default i2cBusNo 1, i2cAddress 0x77.
// 
const options = { i2cBusNo : 1, i2cAddress : BME280.BME280_DEFAULT_I2C_ADDRESS() };

bme280.init(options)
  .then((result) => {
     console.log('BME280 initialization succeeded');
     readSensorData();
  })
  .catch((err) => console.error('BME280 initialization failed: ' + err));

const readSensorData = () => {
  bme280.readSensorData()
    .then((data) => {
      // temperature_C, pressure_hPa, and humidity are returned by default.
      // I'll also calculate some unit conversions for display purposes.
      //
      data['temperature_F'] = BME280.convertCelciusToFahrenheit(data.temperature_C);
      data['pressure_inHg'] = BME280.convertHectopascalToInchesOfMercury(data.pressure_hPa);
 
      console.log('data = ' + JSON.stringify(data, null, 2));
    })
    .catch((err) => console.log('BME280 read error: ' + err))

  setTimeout(readSensorData, 2000);
}
```
##Example Output

```
> sudo node example.js          
Found BME280 chip id 0x60 on bus i2c-1 address 0x77
BME280 initialization succeeded
data = {
  "temperature_C": 32.09,
  "humidity": 34.851083883116694,
  "pressure_hPa": 1010.918480644477,
  "temperature_F": 89.76200000000001,
  "pressure_inHg": 29.852410107059583
}
```
##Example Wiring

For setup on a Raspberry Pi, take a look at my [pi-weather-station](https://github.com/skylarstein/pi-weather-station) project.
