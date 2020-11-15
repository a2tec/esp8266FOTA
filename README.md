# esp8266FOTA library for Arduino

## Purpose

A simple library to add support for Over-The-Air (OTA) updates to your project.

## Features

- [x] Web update (requires web server)
- [x] Batch firmware sync
- [x] Force firmware update 

## How it works

This library tries to access a JSON file hosted on a webserver, and reviews it to decide if a newer firmware has been published, if so it will download it and install it.

There are a few things that need to be in place for an update to work.

- A webserver with the firmware information in a JSON file
- Firmware version
- Firmware type
- Firmware bin

## Usage

### Hosted JSON

This is hosted by a webserver and contains information about the latest firmware

```json
{
    "type": "esp8266-fota-http",
    "version": 3,
    "host": "192.168.2.100",
    "port": 80,
    "bin": "/fota/esp8266-fota-http-3.bin"
}
```

#### Firmware types

Types are used to compare with the current loaded firmware, this is used to make sure that when loaded, the device will still do the intended job.

As an example, a device used as a data logger should ony be updated with new versions of the data logger.

### Sketch

In this example a version 1  of 'esp8266-fota-http' is in use, it would be updated when using the JSON example.

```cpp
#include <esp8266fota.h>
#include <WiFi.h>

const char *ssid = "";
const char *password = "";

esp32FOTA esp32FOTA("esp8266-fota-http", 1);

void setup()
{
  esp8266FOTA.checkURL = "http://server/fota/fota.json";
  Serial.begin(115200);
  setup_wifi();
}

void setup_wifi()
{
  delay(10);
  Serial.print("Connecting to ");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED)
  {
    delay(500);
    Serial.print(".");
  }
}

void loop()
{
  bool updatedNeeded = esp8266FOTA.execHTTPcheck();
  if (updatedNeeded)
  {
    esp8266FOTA.execOTA();
  }
  delay(2000);
}
```

