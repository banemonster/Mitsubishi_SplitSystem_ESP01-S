# Mitsubishi_SplitSystem_ESP01-S

The goal was to integrate a Mitsubishi Electric split-system (heat pump/ air conditioner) into Home Assistant
The model chosen was the msz-ap series, specifically the Mitsubishi Electric msz-ap71vgd.
The solution is also compatible with the msz-gs series.

The msz-ap series have a port designed to be used with the official Mitsubishi Electric wifi module.
(see https://www.mitsubishielectric.com.au/product/mac-568if-e-wi-fi-controller/)
This port can also likely be used with a wired wall controller.

This module is expensive at approx $150 AUD and requires cloud (internet) connectivity to function, if this Mitsibishi cloud servers are offline then it will not function.
I was also unsure how this would integrate into Home Assistant.

An alternative way to control this split system with an ESP32/ESP8266 was sought.

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


*****PROTOTYPING*****

Hardware used

  --------------------------------------------
  ESP8266 with Wifi module
  (https://core-electronics.com.au/wifi-module-esp8266-4mb.html?gclid=Cj0KCQjw0oyYBhDGARIsAMZEuMsqEynneGsqAUEmhW5dBhQp8BbSO-jsiZAU37wLrKRTZumEK9Bxl3oaAjbLEALw_wcB)
  --------------------------------------------
  3.3v to 5v Logic Level Converter Bi-Directional
  (2.54mm PIN headers also required)
  <sub>https://core-electronics.com.au/logic-level-converter-bi-directional.html?gclid=Cj0KCQjw0oyYBhDGARIsAMZEuMuJvLYmw5sYBc2f_s8YdNlOhzxnMnitoNQM5rWQFOs-F3umBzUcxvgaArQAEALw_wcB</sub>
  
  Schematic here
  https://dlnmh9ip6v2uc.cloudfront.net/datasheets/BreakoutBoards/Logic_Level_Bidirectional.pdf
  --------------------------------------------
  5v to 3.v step down DV-DC converrter
  Based on AMS1117 
  available on ebay/aliexpress for under a dollar.
  https://lonelybinary.com/products/ams1117-3-3-5v-to-3-3v-step-down-regulator-module-pack-of-20
  --------------------------------------------
  JST, PA Female Connector Housing, 2mm Pitch, 5 Way, 1 Row
  https://au.rs-online.com/web/p/wire-housings-plugs/4766798
  --------------------------------------------
  JST SPHD Crimp Terminal to Unterminated Crimped Wire, 300mm, 0.25mm²
  https://au.rs-online.com/web/p/crimped-wire/5128737
  --------------------------------------------
 
 
 
 
 
  
      2x8 PIN header LCSC part# C2897405
      1x4 PIN header LCSC part# C2897367
      1x3 PIN header LCSC part# C718242
  2x  1x6 PIN header LCSC part# C40877
