# CANmann — CAN Trace Viewer

A desktop/browser tool for inspecting CAN bus logs, in the style of **CANalyzer**: decodes every message using **DBC** files and lets you explore the trace and plot signals over time.

It's a single self-contained HTML file (`can_viewer.html`) — no install, no build step, no external dependencies. Open it straight in the browser and everything runs locally: no loaded file (DBC, CSV, ASC) ever leaves your machine.

## Usage

1. Open `can_viewer.html` in Chrome/Edge (needs `<input type="file">`, drag & drop, Canvas 2D).
2. **DBC files**: load one or more `.dbc` files, one per bus. If the filename includes `CAN1`, `CAN2`, etc., the matching log channel is auto-assigned to that DBC.
3. **CAN log**: load a `.csv` (format `Timestamp,CAN_ID,DLC,Data`, the one `CANmann.py` produces) or an `.asc` (Vector ASCII).
4. Pick the DBC for each channel under **Channel → DBC** if the automatic mapping got it wrong.

## Trace tab

- Virtualized table (handles hundreds of thousands of messages without lag), with filtering by ID/name/channel and a "decoded only" toggle.
- Decoded signal panel when a message is selected: physical value, raw value, unit, `VAL_` table labels, multiplexing, and a warning when a multi-packet J1939 message (e.g. DM1) exceeds the 8 bytes of the captured frame.
- Trace playback with a speed control.
- "Messages seen" list sorted by frequency, with a green/red indicator for decoded vs. unknown.
- "Most variation": ranks signals by how many times their value actually changed — not just how often the message was transmitted.

## Graphs tab

- Pick any DBC-recognized signal (from the message list or the search box) and it's added as its own stacked lane, with its own Y axis.
- Step line (a CAN signal holds its value until the next message).
- Synchronized cursor across every lane, with a readout panel showing each signal's value, DBC description, and the real timestamp of its last message.
- Mouse-wheel zoom and horizontal drag-to-pan; vertical drag to scroll through lanes that don't fit on screen.
- Alternative table view for each signal.

## Supported file formats

| File | Format |
|---|---|
| DBC | Windows-1252, standard syntax (`BO_`, `SG_`, `VAL_`, `CM_`, simple multiplexing) |
| Log | `.csv` (`Timestamp,CAN_ID,DLC,Data`) or `.asc` (Vector ASCII, multi-channel) |

Bit-level decoding (Intel/Motorola, signed values, multiplexing, multi-packet message truncation) was validated against [`cantools`](https://github.com/cantools/cantools) before the first version shipped.

## Known limitations

- Doesn't read raw `.blf` binary files — Vector BLF uses compressed containers in a proprietary format. Convert to `.csv` or `.asc` first (for example with `python-can`).
- Built for desktop Chrome/Edge; not optimized for mobile.
