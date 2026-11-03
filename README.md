# UDI Polyglot v2 Philips Hue Poly 

## Why this fork exists

This fork builds on xKing's Philips Hue node server. It addresses gaps found while investigating missing status and lost control in a PG3x/eISY installation:

- An eISY Query did nothing for several node types. Query now re-reports their cached driver values. It does not fetch a new reading from the bridge.
- A silent or stalled event-stream connection could leave status updates stuck. The stream now has a one-hour read timeout. If its thread exits, the next poll starts it again and refreshes device states to catch changes missed during the gap. Active streams are not periodically interrupted.
- Bridge requests now have connection and read timeouts, so a request cannot wait indefinitely.

These changes give the plugin a defined recovery path and make status queries useful. Stream timeout/restart behavior was observed, and two reachable lights passed on/off tests through eISY with live bridge-event feedback and restoration of their original state. This is evidence of current control and recovery behavior, not a guarantee of indefinite stability. The original cause of the reported loss of control remains unproven.

### Diagnostic work and limits

This branch also includes diagnostic code for private groups, entertainment configurations, older v1 groups and v2 resource cross-references. That work is not a claim that every group can be named correctly or that new device types are supported. Diagnostic logs may include bridge resource names and identifiers; review them before sharing publicly. An intermittent eISY HTTP 404 is not claimed as fixed by this fork.

[![license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/exking/udi-hue-poly/blob/master/LICENSE)
[![Build Status](https://travis-ci.org/exking/udi-hue-poly.svg?branch=master)](https://travis-ci.org/exking/udi-hue-poly)

This Poly provides an interface between Hue Bridge and [Polyglot v2](https://github.com/UniversalDevicesInc/polyglot-v2) server.
All Philips branded bulbs are supported (Dimmable, White, Color and Extended Color),
bulbs could be added to the Insteon scenes as responders and should respond to all basic commands, including dimming.

### Installation instructions
Make sure that you have a `zip` executable on the system, install using your OS package manager if necessarily.
You can install NodeServer from the Polyglot store or manually running
```
cd ~/.polyglot/nodeservers
git clone https://github.com/exking/udi-hue-poly.git Hue
```

### Configuration

Once installed - push the button on the Hue Bridge and add NodeServer into Polyglot, that will allow it to pair with the Bridge.
If you have been using v1 of the Hue Poly - you will likely have a file named `bridges`, copy that file into `~/.python_hue` - you should be able to skip the pairing process and start this Poly out of the box.

Sometimes automatic bridge IP address discovery fails, in that case - you can specify Bridge IP address in a custom parameter `ip` for the NodeServer using Polyglot's frontend interface. Additionally - if `username` parameter is given - NodeServer will use it (in case you have a username established already and don't want another one created on the Bridge).

### Notes

Poly assumes that Bridge IP address never change, so it is recommended that you create an IP address reservation for the Hue Bridge on your router.

Please report any problems on the [UDI user forum](https://forum.universal-devices.com/topic/23149-polyglot-v2-hue-nodeserver/).

Thanks and good luck.

### History
1. [Phue](https://github.com/studioimaginaire/phue) Library with minor modifications is used as a backend.
2. [Original Implementation](https://github.com/UniversalDevicesInc/Polyglot) of the Hue Poly by UniversalDevices team was a base.
3. [LiFX Poly](https://github.com/Einstein42/udi-lifx-poly) was used as a template in order to re-write this Poly for [Polyglot v2](https://github.com/UniversalDevicesInc/polyglot-v2)
