# First use: integration and Lovelace card

This guide targets the standard Harman Kardon SoundSticks 5. The integration controls the speaker; the Lovelace card provides the dashboard UI. The card is installed and updated together with the integration.

## 1. Prepare Home Assistant and the speaker

- Minimum Home Assistant version: 2025.12.2.
- The bundled integration icon requires HA 2026.3 or newer; older versions simply don't show the icon — control features are unaffected.
- HA needs a local Bluetooth adapter or Bluetooth proxy that can establish GATT connections; proxies that only forward advertisements are not enough.
- Keep the speaker awake and within Bluetooth range.
- This integration does not transport audio. Your phone, computer, or another audio solution plays music to the speaker. Music Assistant is not a requirement for installing this integration.

## 2. Install the BLE integration

Add the repository to HACS as a custom repository:

```text
https://github.com/neqq3/soundsticks5-ha
```

Pick the **Integration** category, download, and restart HA. Accept the discovered SoundSticks 5 under "Settings → Devices & services", or click "Add integration" and search for SoundSticks 5.

Without HACS, copy the repository's `custom_components/soundsticks5` directory to `/config/custom_components/soundsticks5`, restart, and add the integration.

After adding it, first check on the device page that lighting, media, EQ, and other entities appear. Run "Refresh all states" and verify that values such as volume and theme are read back.

## 3. Install the custom card

Once the integration is installed and configured, the card JS and image are already bundled inside the integration directory — nothing needs to be copied to `www`. In the usual UI-managed resources mode, the integration registers the card automatically and bumps the resource version on upgrades.

Refresh your browser. Open the dashboard where you want the card, click edit → add card, and pick "Custom: SoundSticks 5 (HK design)". You can also use a manual card with:

```yaml
type: custom:soundsticks5-lovelace-card
```

The device detail page is not replaced by installing the card, and no dedicated dashboard is created automatically.

If you want this card to be the first thing you see when opening HA, create your own dashboard, add the card, and set it as your default dashboard in your profile's dashboard setting; admins can also set a default dashboard under "Settings → Dashboards". See [the official HA dashboard docs](https://www.home-assistant.io/dashboards/dashboards/#setting-a-default-dashboard).

### Migrating from older manual installs

The legacy resource `/local/soundsticks5-lovelace.js` (including version query parameters) is migrated automatically to the bundled address, and duplicate card resources are merged; existing card configs keep working. Once the new card renders correctly, you can delete the old `www/soundsticks5-lovelace.js`. Keep the old image if a custom `image` config still references it.

### Managing resources with YAML

The integration never rewrites your YAML files. Add the following entry to your existing `lovelace.resources` list; if a legacy resource exists, replace it:

```yaml
lovelace:
  resources:
    - url: /soundsticks5/frontend/soundsticks5-lovelace.js
      type: module
```

Reload resources and refresh your browser. This version-less URL does not set long-lived HTTP caching. If automatic registration fails, you can also add the same address manually on the UI's resources page, type JavaScript module.

## 4. Choose a playback mode

With a single speaker and the goal of controlling volume and lighting, the minimal setup above is enough. The SoundSticks entities are discovered by default and playback mode is `auto`.

### BLE-only info and playback control

In the card's visual editor pick "BLE only", or use:

```yaml
type: custom:soundsticks5-lovelace-card
playback_mode: ble
```

Track info depends on what the speaker actually reports back. Playing over Bluetooth does not guarantee HA receives title/artist; when nothing arrives, "Track information unavailable" is shown.

### Use an existing media player for track info and playback control

If the matching player in MA or another integration already displays track info correctly, pick "Specific media entity" in the card editor and select the actual playback source:

```yaml
type: custom:soundsticks5-lovelace-card
playback_mode: entity
playback_entity: media_player.your_speaker
```

Replace the example ID with your own entity. Title, artist, state, and previous/play/pause/next all use it; **speaker volume, lighting, and EQ are still controlled through the SoundSticks integration**. Playback buttons are disabled when the selected player is unavailable, and no other device is touched.

### Auto mode

```yaml
type: custom:soundsticks5-lovelace-card
playback_mode: auto
playback_entity: media_player.your_speaker
```

Auto uses the selected playback source while it is playing/paused, and falls back to BLE otherwise. With no playback entity configured, it only accepts a single other media player under the same HA device as the BLE media entity; devices created by different integrations may not auto-associate, so MA users usually need to pick once manually.

Auto mode does not detect the speaker's current AUX/Bluetooth input, and never guesses playback sources from similar names. For the full rules, see [playback source configuration](../soundsticks5-card/PLAYBACK.md). Validation currently focuses on a single speaker — treat multi-speaker auto-detection as unsupported.

## 5. Save and apply presets

1. Set volume, lighting effect, EQ, and other parameters the way you like.
2. Enter a name under "Scene presets" and click "Save current". Saving with the same name overwrites.
3. Later, pick a preset from the dropdown and click "Apply". Selecting a name alone does not change the speaker.

Presets store speaker settings, not songs, MA queues, or card layout. They can be called from automations or from the action box in Developer tools:

```yaml
action: soundsticks5.save_preset
data:
  name: Night listening
```

Use `soundsticks5.apply_preset` to apply and `soundsticks5.delete_preset` to delete. These actions currently operate on the first loaded SoundSticks integration instance. Applying writes parameters one by one; a failure midway may leave them partially applied.

## 6. Updates and common issues

| Symptom | What to check |
|---|---|
| Custom element not found | Confirm the integration is loaded, refresh the browser; check the resource list or logs, and register the resource manually with the address above if needed |
| Card image missing | Confirm the integration's `frontend` directory is complete; opening `/soundsticks5/frontend/soundsticks5.png` on your HA instance should show the image |
| Old UI after update | Update the integration, restart HA, then refresh the browser; UI-managed resource versions update automatically — no need to re-add |
| Audio plays but no track info | Check the media entity's `media_title` and `media_artist`; if the correct data lives in another player, configure "Specific media entity" |
| Playback buttons disabled | Check whether the selected player is available and declares support for the operation |
| Lighting or volume uncontrollable | Check that the speaker is awake, BLE distance, and that the adapter/proxy can establish connections; if another app holds the connection, close it and refresh state |

When you see "Bluetooth BLE control failed: connection interrupted or speaker not responding", HK One or another app may be holding the Bluetooth BLE control connection. Fully quit those apps, wait a few seconds, and retry. The same error can also come from signal issues, the speaker standby, or a proxy problem — the message alone does not prove app interference. This refers to the Bluetooth BLE *control* connection; the Classic Bluetooth audio connection used for music playback does not need to be disconnected.

Validate lighting, volume, playback control, and one preset save/apply in your own environment before relying on them in automations. See [validation status](validation.md) for the verified range of adapters and firmware.
