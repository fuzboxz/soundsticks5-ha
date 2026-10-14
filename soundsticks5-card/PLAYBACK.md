# Playback source configuration

Edit the card on an HA dashboard: the visual editor selects the playback control mode and playback source entity. Configuration is stored in the dashboard and shared by all browsers.

- `entity` (specific media entity): title, artist, state, previous, play, pause, and next all come from the selected entity. Buttons are disabled while it is unavailable; the target is never switched automatically.
- `auto` (default): uses the selected playback source while it is playing/paused, and falls back to BLE otherwise. With no playback entity configured, it only accepts a single other media entity that shares the same HA `device_id` as the BLE media entity; it never guesses by name, and falls back to BLE when multiple candidates exist. This is not AUX/Bluetooth input detection.
- `ble`: all playback info and playback control use only the SoundSticks BLE media entity.

Speaker volume always uses the BLE media entity. Lighting, EQ, and device settings keep using the SoundSticks integration.

Example configuration for a specific playback source (replace with your own entity ID):

```yaml
type: custom:soundsticks5-lovelace-card
playback_mode: entity
playback_entity: media_player.your_speaker
```

The optional `media_player` field explicitly selects the **BLE** media entity — it is not an MA playback source. Existing overrides for other entities keep working.

The specific mode never mixes title/artist from two entities. Placeholders are shown when metadata is missing; operations the player does not declare support for are disabled.

Validation: `node tools/playback_test.cjs` covers metadata, MA pause/resume/track-change service routing, BLE volume, offline/capability limits, auto fallback, and multi-candidate handling. A pause and resume round-trip has been tested once on HAOS.
