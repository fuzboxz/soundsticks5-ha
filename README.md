# SoundSticks 5 for Home Assistant

English | [简体中文](README_zh-CN.md)

An unofficial Home Assistant integration for local BLE/GATT control of the Harman Kardon SoundSticks 5.

This project is not affiliated with, authorized, sponsored, or endorsed by Harman Kardon or HARMAN International. Protocol work currently targets the standard SoundSticks 5, HK One 2.5.4, and one tested device; regional, SKU, and firmware differences may still exist.

## Features

- Home Assistant Bluetooth auto-discovery, without persisting historical MAC/RPA values;
- lighting on/off, `0..100` brightness, six lighting themes, animation speed, current theme color, and per-theme color reset;
- seven App-scale EQ sliders with a reset-to-zero action;
- feedback tone, auto-off configuration, and remaining-time state;
- BLE media play/pause/previous/next, absolute volume, plus title and artist state reported by the speaker;
- one-shot full state refresh and one-click release of the GATT control connection;
- serialized GATT access, notification subscription, authoritative media readback, timeout/backoff retries, and diagnostics;
- English and Chinese UI, HACS metadata, and CI.

"Lighting" refers only to the lights, not to speaker power. A BLE media command ACK also does not prove the player has acted; the integration reads aggregate state back after media commands. A2DP audio transport and waking the speaker are intentionally outside this integration's user-facing scope — pair it with a mature Bluetooth audio solution supported by Music Assistant if needed.

## Installation

**First time? Start with [From install to the Lovelace card](docs/getting-started.md)** — it covers default behavior, playback source selection, presets, and common issues.

Detailed steps are in the [installation guide](docs/installation.md). In short:

1. Install via a HACS custom repository, or copy `custom_components/soundsticks5` to `/config/custom_components/`.
2. Restart Home Assistant.
3. Wake the speaker and accept the Bluetooth discovery under Settings → Devices & services, or add SoundSticks 5 manually.

Regular users only need a Bluetooth adapter managed by the HA host. BLE and Bluetooth Classic are two independent protocol layers, but by design they can share one physical adapter. Actual concurrency still depends on the adapter, BlueZ, proxy type, and speaker state.

## Custom card

This repository ships a [SoundSticks 5 custom card](soundsticks5-card/README.md) that is installed and updated together with the integration. It covers lighting effects, volume, EQ, presets, and playback source selection, and supports a specific media entity plus auto or BLE-only playback modes. The card and image are installed and updated with the integration, the resource is registered automatically when resources are managed in the UI, and the card can be added after a browser refresh. For YAML resource setup, see the getting-started guide.

Full 380px narrow preview (0.9.1, example state):

<img src="docs/images/soundsticks5-card-380px.png" alt="Full narrow preview of the SoundSticks 5 custom card, including playback controls, lighting effects, equalizer, scene presets, and product settings" width="380">

## Entities

| Group | Entities |
|---|---|
| Media | Media player: play, pause, previous/next, `0..100` volume, and the media info reported by the device |
| Lighting | Light, theme, speed, current theme color, color reset |
| EQ | 125/250/500/1000/2000/4000/8000 Hz, EQ reset |
| Device settings | Feedback tone, auto-off, remaining time |
| Session | Refresh all states, release BLE control |
| Diagnostics | BLE state, RSSI, last error type |

Full descriptions and scale mappings are in the [entities documentation](docs/entities.md).

## Safety and scope

There is no arbitrary GATT write surface, and OTA, firmware transfer, factory reset, unbinding, and unknown commands are not implemented. Diagnostics redact BLE addresses, API tokens, media URLs, titles, and artist names. Nearby device names, addresses, and manufacturer data are never uploaded.

Firmware version has so far only been observed in the official app UI — there is no safe, stable, and confirmed BLE query for it, so no firmware entity is faked. Rename has a confirmed write frame, but this release ships no entity for it because equally reliable readback and caching semantics are still missing.

## Status

Protocol encoding, parsing, state merging, connection lifecycle, and entity behavior are covered by automated tests. Different HA Bluetooth adapters, remote proxies, and firmware combinations still need continuous validation — see [validation status](docs/validation.md).

Protocol evidence and reproduction tools live in [soundsticks5-protocol](https://github.com/neqq3/soundsticks5-protocol). When reporting issues, include your standard/regional/SKU/firmware variant, HA installation type, and Bluetooth path — but do not publish your device serial number.

## License

Apache License 2.0. See [LICENSE](LICENSE).
