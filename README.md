# Progressor training

A single-file web app for training with a **Tindeq Progressor** force sensor. It connects over Bluetooth, tracks left and right hand peaks, runs a few test types, and keeps a history with progress charts.

No build step, no dependencies and no server. Open `index.html` in Chrome.

> Not affiliated with Tindeq. This is an independent project built on Tindeq's public Bluetooth API.

## Requirements

- **Chrome on Android, or desktop Chrome/Edge.** The app uses Web Bluetooth, which iPhone and Safari do not support.
- The page must be served from a secure origin (HTTPS, e.g. GitHub Pages) or opened as a local file. Bluetooth is blocked inside some embedded frames.
- A Tindeq Progressor (tested against the published API only; see Limitations).

## Using it

1. Wake the Progressor (green flash) and tap **Connect**.
2. **Live**: current force, a force meter and a 10 second graph. Zero (tare), Restart data, Reset max.
3. **Test**: choose Left or Right at the top. With *Peak force* selected there is no Start button: the selected hand's peak updates as you pull. Tap **Save** to store both peaks as one session.
   Other tests (endurance, RFD, repeaters, 7 s hold, critical force) use *Start test* and then *Save this result*.
4. **Progress**: left and right over time, filter by test, period, tag and unit (kg or % body weight). A second chart shows the left/right gap with ±10% guide lines.
5. **Best**: personal bests per hand, best total session, most balanced session, top 10.
6. **History**: one card per session with both hands, tag, note, delete and edit note.

Nothing is saved until you tap Save. Tags default to `Low 22mm`; change `DEFTAG` in the script to change that.

## Data and backups

History is stored in the browser (`localStorage`), not in the file. Replacing `index.html` with a newer version keeps it, as long as you open the app from the same address.

Clearing the browser's site data deletes it, so:

- **Save backup** downloads a JSON file. **Import or restore** reads it back.
- Optionally, every Save also downloads a dated backup file.
- **Import or restore** also reads the Tindeq app's left/right peak force CSV export. Duplicates are skipped (same date to the minute, hand, test and tag).
- **Export Tindeq CSV** writes the same columns as the official export.

If the page is opened as an artifact inside Claude, history also syncs to the signed-in account; this is ignored elsewhere.

## Tests

| Test | What it does |
| --- | --- |
| Peak force | Always on for the selected hand; optional smoothing (off, 0.1 s, 0.25 s) |
| Endurance | Fixed duration, reports average, peak and last-5-seconds average |
| RFD | Max rate of force development and force at 100 ms and 200 ms |
| Repeaters | Configurable work, rest, reps and sets |
| 7 s hold | Average and peak over 7 seconds |
| Critical force | 24 x 7 s on / 3 s off; critical force is the mean force over the last 6 reps, W' is the impulse above it |

## Limitations

- Developed without access to a physical Progressor. Bluetooth behaviour and the endurance, RFD, 7 s hold and critical force numbers have not been verified against the official app.
- The critical force definition is a simplification of published protocols.
- %BW for results saved without a body weight uses the body weight currently entered.
- Single hand of a session cannot be deleted on its own; delete the session.

## Bluetooth

Uses the public Tindeq Progressor API: service `7e4e1701-1ea6-40c9-9dcc-13d34ffead57`, control point `...1703...`, data point `...1702...`. Commands used: tare `0x64`, start `0x65`, stop `0x66`, battery `0x6F`. Weight samples arrive as a float32 kg value plus a uint32 microsecond timestamp.

## Development

Everything is in `index.html` (HTML, CSS and JavaScript). Edit it and reload. The version string is `VERSION` near the top of the script; bump it with each change so you can tell which copy you are running.
