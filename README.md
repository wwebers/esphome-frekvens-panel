# esphome-frekvens-component

A custom component for esp-home to interface with IKEA's Frekvens Cube.

## Sources

This repo is heavily based on the original [FrekvensPanel library by @frumperino](https://github.com/frumperino/FrekvensPanel).

It's also inspired from the esphome component PCD8544.

## Usage

Currently there is a dependency upon Adafruit GFX library. In your esphome config, add these lines:

- Declare necessary libraries
- Define frekvenspanel component

Here is a short config to demonstrate the usage to display time on panel:

```yaml
esphome:
  name: frekvens8266-weather
  platform: ESP8266
  board: d1_mini
  platformio_options:
    upload_speed: 115200
    lib_ldf_mode: deep
    lib_deps:
      - me-no-dev/ESPAsyncTCP
      - adafruit/Adafruit GFX Library   # Required for FrekvensPanel.
      - adafruit/Adafruit BusIO         # Required for FrekvensPanel.
      - Wire                            # Also required by GFX.
      - SPI                             # Also required by GFX.

external_components:
  - source:
      type: local
      path: components

light:
  - platform: monochromatic
    name: 'Brightness'
    output: matrix_brightness
    restore_mode: RESTORE_DEFAULT_ON

output:
  - platform: esp8266_pwm
    # Enables brightness control.
    id: matrix_brightness
    pin:
      number: GPIO14
      inverted: True

time:
  - platform: sntp
    id: ntp_time
    timezone: 'Europe/Berlin'

font:
  - file: "04B03.ttf"
    id: b03
    size: 8

display:
  - platform: frekvens_panel
    latch_pin: 00
    clock_pin: 13
    data_pin: 12

    lambda: |-
      auto time = id(ntp_time).now();
      it.printf(0, 0, id(b03), "%d:%d", time.hour, time.minute);

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "PMSH8ffFaImAL3xxxxsNlv8yQmYkBE="

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Esphome-Web-162032"
    password: "vNaeFgw54QJw"

```

## License
[TBD] the original library does not specify the license. This repo is consecutively not licensed yet either.
