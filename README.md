# homebridge-nut
> A NUT (Network UPS Tools) Plugin for [Homebridge](https://github.com/nfarina/homebridge) leveraging [node-nut](https://github.com/skarcha/node-nut). 

This plugin allows you to monitor a single UPS with HomeKit via a NUT Client.

NOTE: This is re-write of https://github.com/ToddGreenfield/homebridge-nut to avoid usage of `system-sleep` and 
`deasync` module dependencies which end up relying on native code. I was having problems with these blocking the
Node event loop (possibly due to running on FreeBSD). Also I wanted to avoid the need of having to do native code rebuilds...

NOTE: This implementation only supports a single UPS as this is sufficient for my needs and simplified the code somewhat...

## Installation
1. Install Homebridge using: `npm install -g homebridge`
1. Install this plugin using: `npm install -g git+https://github.com/vectronic/homebridge-nut.git`
1. Update your configuration file. See a sample `config.json` snippet below.
1. Ensure you have a NUT Client running somewhere.

This plugin will create an accessory for the first UPS returned from the NUT Client.

The accessory will have a `ContactSensor` service and a `BatteryService`.

The `ContactSensor` will have the following characteristics:

* `ContactSensorState` will be open if UPS Status starts with `OB` (On Battery).
* `StatusActive` will be true if UPS Load is > 0.
* `StatusFault` will be true if NUT is not reachable.

The `BatteryService` will have the following characteristics:
 
* `BatteryLevel` will show the battery charge percentage.
* `ChargingState` will show Charging, Not Charging (Online and 100% battery charge), or Not Chargeable (On Battery).
* `StatusLowBattery` will be true if *BatteryLevel* is below `low_batt_threshold`.

## Configuration

Example `config.json` entry:

```
"platforms": [
  {
    "platform": "Nut",
    "host": "localhost",
    "port": 3493,
    "low_batt_threshold": 40,
    "poll_interval": 60
  }
]
```

Where:

* `host` is the IP or hostname for the Nut Client. Default is `localhost`.
* `port` is the port for the Nut Client. Default is `3493`.
* `low_batt_threshold` is the	battery level percentage at which to set the Low Battery Status to true. Default is 40.
* `poll_interval` is the UPS polling interval in seconds. Default is 60.
