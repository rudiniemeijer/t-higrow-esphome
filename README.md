![t-higrow in action.png](https://github.com/rudiniemeijer/t-higrow-esphome/blob/36cfcc733363cfb44e0d04b88e12d74eab741e77/t-higrow%20in%20action.png)

# T-HiGrow for ESPhome and Home Assistant

The LilyGo T-HiGrow device has been designed to work on a single LiPo battery. The incorporated ESP32 microcontroller has a deep sleep mode that is supported by ESPhome. Getting the sensors to work after deep sleep has proven to be something of a challenge. This configuration file is a succesful implementation of getting all the sensors to work reliably, while reducing the power to almost nothing once in deep sleep.

## Sensors, conversions and calibration
The T-HiGrow has a number of sensors, some of which can be extended by conversions or state observations. Below, the sensors and possible conversions are discussed.

### DHT11 temperature and air humidity sensor
The DHT11 sensor is factory calibrated and outputs serial data through a single data pin. The data pin from the DHT11 is connected to GPIO16 of the ESP32 microcontroller. The output given by the data pin is 8 bit humidity integer data + 8 bit the humidity fractional data + 8 bit temperature integer data + 8 bit fractional temperature data + 8 bit parity bit. The DHT11 has been specified with an operating current of 0.3 mA and a standby current of 60 µA. Its temperature range is 0 °C - 50 °C and its humidity range 20% to 90%, with a ±1 °C and ±1 % accuracy.

[DHT11 data sheet](https://akizukidenshi.com/download/ds/aosong/DHT11.pdf)

### BH1750 illuminance sensor
The BH1750 sensor measures light and has a I2C interface. The SDA pin is connected to GPIO25 and the SCL pin is connected to GPIO26.

[BH1750 data sheet](http://www.mouser.com/ds/2/348/bh1750fvi-e-186247.pdf)

### ADC battery voltage
Battery voltage is supplied to GPIO33. The ADC has a reference voltage of 1.1 V, giving a theoretical range of 0-1.1 V as input. However, [Espressiv states](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/adc.html#_CPPv425adc1_config_channel_atten14adc1_channel_t11adc_atten_t) that the input voltage should be in range 0.1 - 0.950

| Description | Charging | Voltage |
| ----------- | -------- | ------- |
| Empty       |       No |  2.55 V |
| Full        |       No |  3.65 V |
| Empty       |      Yes |  3.93 V |
| Full        |      Yes |  4.24 V |

### ADC capacitive soil moisture

### ADC conductivity

## ESPhome configuration

### General apporach

### Deep Sleep workarounds

## Home Assistant integration
![A possible display of the T-HiGro entities in Home Assistant](https://github.com/rudiniemeijer/t-higrow-esphome/blob/a93519f81fea08ca37844c24e619a53078a01fc5/possible%20home%20assistant%20display.png)



