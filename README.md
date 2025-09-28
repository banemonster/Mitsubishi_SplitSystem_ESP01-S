# Mitsubishi_SplitSystem_ESP01-S

The goal was to integrate a Mitsubishi Electric split-system (heat pump/ air conditioner) into Home Assistant
The model chosen was the msz-ap series, specifically the Mitsubishi Electric msz-ap71vgd.
The solution is also compatible with the msz-gs series.

The msz-ap series have a port designed to be used with the official Mitsubishi Electric wifi module.
(see https://www.mitsubishielectric.com.au/product/mac-568if-e-wi-fi-controller/)
This port can also likely be used with a wired wall controller.

This module is expensive at approx $150 AUD and requires cloud (internet) connectivity to function, if this Mitsibishi cloud servers are offline then it will not function.
I was also unsure how this would integrate into Home Assistant.

An alternative way to control this split system with an ESP32/ESP8266 was sought
This solution has been made possible following 

[github://geoffdavis/esphome-mitsubishiheatpump](https://github.com/geoffdavis/esphome-mitsubishiheatpump)

*****CN105 Port*****

The MSZ-AP provides 12v and 5v on the CN105 connector
This is the connector we will use

The CN105 connecctor consists of 5 pins.

    | Rx | TX | 5v | GND | 12v |

We have no use for the 12v connection so this is not used.
The ESP8266 runs on 3.3v so we need to do two things
  1. Step down the 5v to 3.3v to power the ESP8266
  2. Use a 3.3v to 5v bi-direction logic converter
     This will allow the ESP8266 to communicate with the split-system by boosting/dropping the Rx/TX during communications.

The ESP8266 is adopted into ESPHOME --> Home Assistant.

PROTOTYPING
----------------------------

**Hardware used**


**ESP8266 with Wifi module**


<img src="https://github.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/blob/main/images/ESP8266.jpg" width="200" height="200">


<sub>https://core-electronics.com.au/wifi-module-esp8266-4mb.html?gclid=Cj0KCQjw0oyYBhDGARIsAMZEuMsqEynneGsqAUEmhW5dBhQp8BbSO-jsiZAU37wLrKRTZumEK9Bxl3oaAjbLEALw_wcB</sub>

**3.3v to 5v Logic Level Converter Bi-Directional**

<img src="https://github.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/blob/main/images/logic_level.jpg" width="200" height="200">

<sub>https://core-electronics.com.au/logic-level-converter-bi-directional.html?gclid=Cj0KCQjw0oyYBhDGARIsAMZEuMuJvLYmw5sYBc2f_s8YdNlOhzxnMnitoNQM5rWQFOs-F3umBzUcxvgaArQAEALw_wcB</sub>

Schematic here

<sub>https://dlnmh9ip6v2uc.cloudfront.net/datasheets/BreakoutBoards/Logic_Level_Bidirectional.pdf</sub>

**5v to 3.v step down DC-DC converter**

<img src="https://github.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/blob/main/images/5v_3.3.JPG" width="200" height="200">

Based on AMS1117 

available on ebay/aliexpress for under a dollar.

<sub>https://lonelybinary.com/products/ams1117-3-3-5v-to-3-3v-step-down-regulator-module-pack-of-20</sub>

**JST, PA Female Connector Housing, 2mm Pitch, 5 Way, 1 Row**

<img src="https://github.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/blob/main/images/PAP05.JPG" width="200" height="200">

<sub>https://au.rs-online.com/web/p/wire-housings-plugs/4766798</sub>

**JST SPHD Crimp Terminal to Unterminated Crimped Wire, 300mm, 0.25mm²**

<img src="https://github.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/blob/main/images/JST%20SPHD.JPG" width="200" height="200">

<sub>https://au.rs-online.com/web/p/crimped-wire/5128737</sub>

 
 
**Prototype assembly**
 
 ![image](https://user-images.githubusercontent.com/90736990/186046008-7ee608f8-2d50-43dd-92d6-24fb9485d198.png)

 
**Prototype working**

 ![image](https://user-images.githubusercontent.com/90736990/186046143-faeb23c3-6c2f-44f7-b272-0d68b74b6b31.png)

 **Prototype config**
 
 The following config has been deployed via ESPHOME
```
 esphome:
  name: esp-01s-1

esp8266:
  board: esp01_1m

# Enable logging
logger:
  # ESP8266 only - disable serial port logging, as the HeatPump component
  # needs the sole hardware UART on the ESP8266
  baud_rate: 0
  
# Enable Home Assistant API
api:

ota:
  - platform: esphome
    password: "91edd67242043c502dba027797f4f21a"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Esp-01S-1 Fallback Hotspot"
    password: "4gRboFYbND9N"

captive_portal:




#code below
substitutions:
  name: esp-01s-1
  friendly_name: Master Mitsubishi AC



# Note: if upgrading from 1.x releases of esphome-mitsubishiheatpump, be sure
# to remove any old entries from the `libraries` and `includes` section.
#libraries:
  # Remove reference to SwiCago/HeatPump

#includes:
  # Remove reference to src/esphome-mitsubishiheatpump





# Enable Web server.
web_server:
  port: 80

  # Sync time with Home Assistant.
time:
  - platform: homeassistant
    id: homeassistant_time

# Text sensors with general information.
text_sensor:
  # Expose ESPHome version as sensor.
  - platform: version
    name: ${name} ESPHome Version
  # Expose WiFi information as sensors.
  - platform: wifi_info
    ip_address:
      name: ${name} IP
    ssid:
      name: ${name} SSID
    bssid:
      name: ${name} BSSID

# Sensors with general information.
sensor:
  # Uptime sensor.
  - platform: uptime
    name: ${name} Uptime
    update_interval: 3600s

  # WiFi Signal sensor.
  - platform: wifi_signal
    name: ${name} WiFi Signal
    update_interval: 3600s

external_components:
  - source: github://geoffdavis/esphome-mitsubishiheatpump


climate:
  - platform: mitsubishi_heatpump
    name: "${friendly_name}"

    # ESP32 only - change UART0 to UART1 or UART2 and remove the
    # logging:baud_rate above to allow the built-in UART0 to function for
    # logging.
    hardware_uart: UART0
    #baud_rate: 9600
    supports:
      mode: [HEAT_COOL, COOL, HEAT, FAN_ONLY]
      fan_mode: [AUTO, LOW, MEDIUM, MIDDLE, HIGH]
      swing_mode: ["OFF", "VERTICAL"]
    visual:
      min_temperature: 16
      max_temperature: 31
      temperature_step: 0.5
```
  
  
Updated version
------------

Now that the prototype has been proven, the next step is to refine the solution.
This was done by creating a PCB to accomodate the discrete components used in the prototype.

The PCB was created using Easy EDA.

https://github.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/blob/main/mitsubishi-ac-with-ESP-01s.pdf

![image](https://user-images.githubusercontent.com/90736990/186047070-388a2cfe-2dbe-4e86-89c5-8effc9133bb1.png)

**BOM**

![image](https://user-images.githubusercontent.com/90736990/186047468-c2abb91b-aa26-4b1e-a27d-fbaef604920b.png)

![image](https://user-images.githubusercontent.com/90736990/186047845-78a2f905-c9c5-44ad-bd4e-6e2520daefea.png)



Further revision
-------------
A new version was designed with an embedded ESP-12S however this failed to go into programming mode.
There appears to be an error in the PCB design.

![image](https://user-images.githubusercontent.com/90736990/186048632-fd1ddc54-288e-42f6-86ad-d28587d7cfc2.png)




Example of dashboard layout
-------------

![image](https://raw.githubusercontent.com/banemonster/Mitsubishi_SplitSystem_ESP01-S/refs/heads/main/images/dashboard_example_1.png)
