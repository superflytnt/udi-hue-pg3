# UDI PG3x Philips Hue NodeServer

## Why this fork exists

This fork builds on xKing's Philips Hue node server. It addresses gaps found while investigating missing status and lost control in a PG3x/eISY installation:

- An eISY Query did nothing for several node types. Query now re-reports their cached driver values. It does not fetch a new reading from the bridge.
- A silent or stalled event-stream connection could leave status updates stuck. The stream now has a one-hour read timeout. If its thread exits, the next poll starts it again and refreshes device states to catch changes missed during the gap. Active streams are not periodically interrupted.
- Bridge requests now have connection and read timeouts, so a request cannot wait indefinitely.

### Version history

- **1.1.1** (2026-11-07): Heartbeat pings throttled to at most one per 60 seconds, to reduce rapid-ping HTTP 429 responses; monitor-specific limits still apply.
- **1.1.0** (2026-11-07): External heartbeat - optional `heartbeat_url` custom parameter. The plugin pings it on every poll, regardless of bridge or device state, so an external cron monitor (e.g. UptimeRobot heartbeat) alerts when the plugin itself freezes. Empty (default) disables the ping.
- **1.0.9**: Query re-reports cached driver values; event-stream one-hour read timeout with poll-driven restart; bounded bridge request timeouts.

These changes give the plugin a defined recovery path and make status queries useful. Stream timeout/restart behavior was observed, and two reachable lights passed on/off tests through eISY with live bridge-event feedback and restoration of their original state. This is evidence of current control and recovery behavior, not a guarantee of indefinite stability. The original cause of the reported loss of control remains unproven.

### Diagnostic work and limits

This branch also includes diagnostic code for private groups, entertainment configurations, older v1 groups and v2 resource cross-references. That work is not a claim that every group can be named correctly or that new device types are supported. Diagnostic logs may include bridge resource names and identifiers; review them before sharing publicly. An intermittent eISY HTTP 404 is not claimed as fixed by this fork.

[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

This NodeServer provides an interface between the Hue Bridge and Polyglot v3 (PG3x) on Polisy or eISY.
Features implemented in the current source include:

- Light on/off, fast on/off, brighten/dim, fades, brightness and transition timing.
- RGB/xy color, named colors, color temperature, alerts and effects where supported by the bridge and light.
- Group control and room/zone scene recall.
- Motion, light-level, temperature and button-event nodes with bridge event-stream updates.
- Multiple bridge configuration, discovery, cached Query responses and stream recovery.

Feature availability depends on the bridge resources and device capabilities. The tests described above cover two lights and stream recovery, not every device or command. Group naming still has the limits described above.

### Installation instructions

### Local installation for development/testing

Public-store availability of this version has not been confirmed. Cloning the repository alone does not register or install a plugin in PG3(x).

Follow [UDI's node server publishing guide](https://wiki.universal-devices.com/Publishing_a_node_server_to_the_node_server_store) to create a **Local** store entry. Use this repository's `server.json` for the plugin fields and add a purchase option with the current version and these installation settings:

- Installation URL: `https://github.com/superflytnt/udi-hue-pg3`
- Branch: `master`
- Executable: `hue.py`
- Install script: `install.sh`

Select the Local store, then install the entry through PG3(x). A Local entry is private to that PG3(x) instance. See [UDI's packaging guide](https://wiki.universal-devices.com/Packaging_a_node_server_for_distribution) for deployment details. Publishing access and any account requirements are handled by UDI.

### Configuration

On first pairing, press the Hue Bridge button and restart the node server within 30 seconds when prompted. The current implementation saves bridge addresses and application keys in PG3 custom data; the old `~/.python_hue` file is not used by this version.

Custom parameters:

| Parameter | Purpose |
|-----------|---------|
| `bridges` | JSON list of bridge IP addresses, for example `["192.168.1.10", "192.168.1.11"]`. If unset, saved bridges are used, then discovery is attempted. |
| `ignore_second_on` | A non-empty value enables ignoring a normal On command when a light is already on. |
| `debug` | A non-empty value enables debug logging. Review logs before sharing. |
| `heartbeat_url` | Optional heartbeat/cron-monitor URL. Pings are attempted on poll callbacks, at most once per 60 seconds, regardless of bridge/device state. Match the monitor interval to the poll cadence; empty disables it. |

The current code reads the `bridges` parameter, not the old `ip` or `username` custom parameters. Keep bridge application keys and private monitoring URLs out of public logs and posts.

### Notes

Reserve each Hue Bridge IP address on your router so saved bridge addresses remain valid.

Please report any problems on the [UDI user forum](https://forum.universal-devices.com/topic/47480-hue-111-status-fixes-stream-recovery-timeouts/).

Thanks and good luck.

### History
1. This version uses the repository's `huev2.py` client for Hue API v2 and event streams. Earlier versions used [Phue](https://github.com/studioimaginaire/phue).
2. [Original Implementation](https://github.com/UniversalDevicesInc/Polyglot) of the Hue Poly by UniversalDevices team was a base.
3. [LiFX Poly](https://github.com/Einstein42/udi-lifx-poly) was used as a template in order to re-write this Poly for [Polyglot v2](https://github.com/UniversalDevicesInc/polyglot-v2)
