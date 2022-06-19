![t-higrow in action.png](https://github.com/rudiniemeijer/t-higrow-esphome/blob/36cfcc733363cfb44e0d04b88e12d74eab741e77/t-higrow%20in%20action.png)

# T-HiGrow for ESPhome and Home Assistant

The LilyGo T-HiGrow ESP32-based plant sensor device has been designed to work on a single LiPo battery. The incorporated ESP32 microcontroller has a deep sleep mode that is supported by ESPhome. Getting the sensors to work after deep sleep has proven to be something of a challenge. This configuration file is a succesful implementation of getting all the sensors to work reliably, while reducing the power to almost nothing once in deep sleep.

- Buy it from the official LilyGo store on AliExpress: https://nl.aliexpress.com/item/32815782900.html
- Or from the Dutch TinyTronics (has some older versions): https://www.tinytronics.nl/shop/nl/development-boards/microcontroller-boards/met-wi-fi/lilygo-ttgo-t-higrow-esp32-dht11-sensor-met-behuizing-en-accu

## References
I have studied (and used some of) the examples and code of the following repositories and publications. While some of them were hulpful, or even mostly usable, none of the examples had a working ESPhome implementation combining deep sleep, calibrated sensor readings and/or an interface to the Home Assistant Plant entity, so it took some combining, calibrating, and testing:

- https://github.com/pesor/TTGO-T-HIGrow
- https://github.com/Xinyuan-LilyGO/LilyGo-HiGrow
- https://gist.github.com/electron64/f4d5c947124febda1a1fc5cb4afe85a7
- https://www.tindie.com/products/lilygo/lilygottgo-t-higrow/
- https://gist.github.com/WoLpH/bc284ba9aeb5d1263f72d6294e239c1a
- https://community.home-assistant.io/t/ttgo-higrow-with-esphome/144053

## T-HiGrow circuit

### Voltages used

| Description | Voltage | Source |
| ----------- | ------- | ------ |
| VBUS        |    5 V  | USB-C  |
| VBAT        |   3.7V - 4.2V  | Lithium Ion battery |
| +5V         |   3.7V - 5V | If VBUS is present, otherwise VBAT |
| VDD3V3      |   3.3V  |        |
|

### Battery charging
VBUS is connected to a TP4054 Linear Li-lon Battery Charger that outputs a constant 4.2 V with a programmed current (using a 2k resistor) of 580 mA. An led signals the charging process; once the led dims, the charging is complete.

### Switching 

## Sensors, conversions and calibration
The T-HiGrow has a number of sensors, some of which can be extended by conversions or state observations. Below, the sensors and possible conversions are discussed.

### DHT11 temperature and air humidity sensor
The DHT11 sensor is a combined household-level air temperature and humidity sensor. It is factory calibrated and outputs serial data through a single data pin. The data pin from the DHT11 is connected to GPIO16 of the ESP32 microcontroller. The output given by the data pin is 8 bit humidity integer data + 8 bit the humidity fractional data + 8 bit temperature integer data + 8 bit fractional temperature data + 8 bit parity bit. The DHT11 has been specified with an operating current of 0.3 mA and a standby current of 60 µA. Its temperature range is 0 °C - 50 °C and its humidity range 20% to 90%, with a ±1 °C and ±1 % accuracy.

[DHT11 data sheet](https://akizukidenshi.com/download/ds/aosong/DHT11.pdf)

### BH1750 illuminance sensor
The BH1750 sensor measures light and has a I2C interface. It provides 16-bit light measurements in lux, specified from 0 to 65K+ lux. The SDA pin is connected to GPIO25 and the SCL pin is connected to GPIO26. ESPhome uses the Arduino Wire library to communicate with the BH1750 over I2C. This library depends on the wire.begin() function to be called in order to power up I2C devices. The BH1750 has its own power saving measures and wakes up once called from I2C. Experimentation shows that after deep sleep from the ESP32, either the I2C communication in general, or the BH1750 specifically, do not respond reliably. That can be corrected by a wire.begin() and some strategically placed waits.

[BH1750 data sheet](http://www.mouser.com/ds/2/348/bh1750fvi-e-186247.pdf)

### ADC battery voltage
Battery voltage is supplied to GPIO33, which is capable of reading an analog voltage using 12-bit analog to digital conversion. The ADC has a reference voltage of 1.1 V, giving a theoretical range of 0 - 1.1 V as input. However, [Espressiv states](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/adc.html#_CPPv425adc1_config_channel_atten14adc1_channel_t11adc_atten_t) that the input voltage should be in range 0.1 - 0.950 V for the maximum accuracy. Also, an internal attenuation of up to 11 dB can be used, that stretches the input range to 0.150 V - 2.450 V. As the lithium battery has an expected battery range from 0 to up to 5 volts while charging, a voltage divider with two 100k resistors is used, that divides the battery voltage in half. The formula used is VBATT = (VADC / 4.095) * 6.6 * VREF, where VADC is the measured voltage, VREF = 1.1 and VBATT is the calculated battery voltage in volts.

| Description | Charging | Voltage |
| ----------- | -------- | ------- |
| Empty       |       No |  2.55 V |
| Full        |       No |  3.65 V |
| Empty       |      Yes |  3.93 V |
| Full        |      Yes |  4.24 V |

[Espressif documentation for ADC measurements]()

### ADC capacitive soil moisture
The sensor electronics for the soil moisture sensor are built around a 555 timer IC, that is setup to form an oscillator where the PCB traces are part of the circuitry that determine the frequency. The soil moisture changes the capacitance and the oscillation frequency changes. The frequency is 

The timer and the other supportive components form an oscillator that works on a set frequency. Since the measuring part of the sensor is made out of two PCB tracks on the board, they act as a capacitor that is connected to this oscillating circuit.

Once the sensor is placed in soil, the moisture in the soil changes the capacitance of this capacitor, and with that, the frequency of oscillation on the 555 timer is changed.

This change in frequency is then translated to an analog voltage from 1 to 3 volts and this can then be detected with an Arduino and translated into a percentage.

### ADC conductivity

## ESPhome configuration

### General apporach

### Deep Sleep workarounds

## Home Assistant integration
![A possible display of the T-HiGro entities in Home Assistant](https://github.com/rudiniemeijer/t-higrow-esphome/blob/a93519f81fea08ca37844c24e619a53078a01fc5/possible%20home%20assistant%20display.png)



