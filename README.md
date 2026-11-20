# BopPad Calibrator

A browser-based tool for diagnosing and comparing KMI BopPad behavior across multiple units. Captures preset configuration via SysEx dump, records velocity response per quadrant and zone, and compares sessions side by side.

## Features

- **Preset dump & save** — reads the full BopPad preset via SysEx (fw 3.x, 448 parameters)
- **Session storage** — saves named sessions to localStorage; persists across page reloads
- **Preset comparison** — diffs any two sessions field by field, highlighting differences
- **Velocity recording** — records hit velocity per quadrant × zone (Z0 center through Z3 outer edge)
- **Velocity comparison** — side-by-side median velocity grid for two sessions with delta, color-coded by magnitude
- **MIDI trace** — columnar live trace showing event, quadrant, zone, velocity, and time delta
- **Raw mode** — bypasses zone CC decode to show all incoming notes directly
- **Factory reset** — sends factory default preset to connected device (with confirmation)
- **Export** — session JSON export and CSV export of velocity data and preset comparison

## Requirements

- **Chrome** (Web MIDI + SysEx support required; Safari not supported)
- **BopPad** connected via USB
- Python 3 (optional, for local HTTP server if Chrome blocks file:// access)

## Usage

### Option 1 — Open directly in Chrome
Open `index.html` in Chrome. If Web MIDI doesn't initialize, use Option 2.

### Option 2 — Local HTTP server
```bash
cd boppad-calibrator
python3 -m http.server 8080
```
Then open `http://localhost:8080` in Chrome.

### Workflow

**Comparing preset configuration:**
1. Connect a BopPad, set a Nickname, select MIDI In and MIDI Out
2. Set the preset on the device using the BopPad Editor
3. Click **Dump & Save** — the preset is decoded and saved as a session
4. Disconnect, connect the next unit, repeat
5. In the Compare section, pick two sessions and click **Compare**

**Comparing velocity response:**
1. Connect a BopPad and set up as above
2. Click **Reset Data** to clear the velocity grid
3. Click **Start Recording**
4. Hit each quadrant in each zone several times with consistent force
5. Click **Stop Recording**
6. Click **Dump & Save** — velocity data is saved with the session
7. Repeat for each unit
8. Compare two sessions — the Velocity Comparison grid appears below the preset diff table

## Configuration

| Field | Description |
|---|---|
| Nickname | Label for this device in sessions and comparisons |
| MIDI In | Input port for the connected BopPad |
| MIDI Out | Output port (required for Dump & Save and Factory Reset) |
| Zone CC# | CC number the BopPad uses to signal hit location (default: 20) |
| Zone Thresholds | CC value boundaries for Z0–Z3 (default: 1, 30, 64, 95) |
| Q1–Q4 Notes | MIDI note numbers for each quadrant (default: 30, 35, 40, 45) |
| Raw mode | Ignore zone CC; record all hits in Z0; show all notes in trace |

## Velocity comparison interpretation

Deltas in the velocity comparison grid are color-coded:
- **Green** — within ±5 (essentially matched)
- **Yellow** — ±6–15 (noticeable difference)
- **Red** — >±15 (significant difference)

The delta shown is **B minus A** (Session B minus Session A).

## Notes

- Preset switching via Program Change was removed — it behaved inconsistently across firmware versions. Set the preset on the device using the BopPad Editor before dumping.
- Sessions are stored in `localStorage` and survive page reloads. Use **Clear All** to reset.
- The **Send to Device** buttons in the Compare section push a full preset SysEx back to the connected device — use with care.
- Tested against BopPad firmware 3.0.0 and 3.0.4.

## License

MIT
