# SoundSticks 5 custom card

A Home Assistant Lovelace card, iterated from a Kimi design mock. It ships with the SoundSticks 5 integration and covers sound, lighting, EQ, device settings, and preset controls.

## Installation

New users should start with the [full getting-started guide](../docs/getting-started.md), which covers HACS installation, card resource registration, default playback behavior, and troubleshooting.

1. Install and configure the `neqq3/soundsticks5-ha` integration in HA.
2. After restarting HA and configuring the integration, the card resource is registered automatically; refresh your browser.
3. Add the SoundSticks 5 custom card to a dashboard:

```yaml
type: custom:soundsticks5-lovelace-card
```

YAML resource management and legacy migration steps are in the [getting-started guide](../docs/getting-started.md). The only source of the release JS and image is `custom_components/soundsticks5/frontend/`; this directory keeps the preview and test tooling.

The card auto-discovers SoundSticks entities. The visual editor supports auto, specific media entity, and BLE-only playback modes — see [PLAYBACK.md](PLAYBACK.md). The product image path can be overridden with the `image` option.

## Presets

Enter a name and click "Save current"; pick a preset from the dropdown and click "Apply" — the same preset can be applied repeatedly. Save/apply/delete are executed by the SoundSticks integration; the card does not change the integration's existing overwrite, storage, or execution rules.

## Local preview and checks

From this directory, run:

```sh
node serve.cjs
node tools/card_smoke.js
node tools/playback_test.cjs
node tools/preset_test.cjs
```

The preview is served at `http://127.0.0.1:8769/` and supports `?width=380` and `?width=640`. The preview uses mock data only.

## Assets and sources

The main line started from a user-provided Kimi design mock and has been evolving since. The product image is the original transparent PNG previously extracted from the HK One app (510 × 360); the six lighting effect tiles are drawn with CSS. Original asset provenance is recorded in [assets/SOURCES.md](assets/SOURCES.md).

Local deployment scripts, credentials, deployment notes, and migration checklists are not committed. The older visual design stays on the local machine as a fallback and is not mixed into this main line.
