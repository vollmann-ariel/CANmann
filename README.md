# CANmann — CAN Trace Viewer

A desktop/browser tool for inspecting CAN bus logs: decodes every message using **DBC** files and lets you explore the trace and plot signals over time.

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
- "Messages seen" list sorted by frequency, with a green/red indicator for decoded vs. unknown, each message's channel and source, and search by any of those (name, ID, or source).
- "Most variation": ranks signals by how many times their value actually changed — not just how often the message was transmitted — showing each signal's channel and source too.

## Graphs tab

- Pick any DBC-recognized signal (from the message list, the "Most active" panel, or the search box — each showing its message, channel, and source) and it's added as its own stacked lane labeled with its message and source, with its own Y axis.
- Step line (a CAN signal holds its value until the next message).
- Synchronized cursor across every lane, with a readout panel showing each signal's value, DBC description, and the real timestamp of its last message.
- Mouse-wheel zoom and horizontal drag-to-pan; vertical drag to scroll through lanes that don't fit on screen.
- Alternative table view for each signal.
- Measure the time between two points: click the ruler button, then click two spots on any chart to see the elapsed time between them.

## DBC Edit tab

- Create a DBC from scratch or open an existing one to edit — independent of the DBC files loaded for decoding in the other tabs.
- Add/edit/delete messages, signals, nodes (`BU_`), and value tables (`VAL_TABLE_`), including start bit, length, byte order, signed/unsigned, factor/offset, min/max, unit, multiplexing, and receivers.
- Per-signal value tables (`VAL_`) with raw-value range validation tied to the signal's own bit width.
- Vector/J1939 attributes (`BA_`/`BA_DEF_`) on messages, signals, and nodes, with hover hints for common CNH/J1939 attribute names.
- Save writes a `.dbc` file (Windows-1252 encoded) via the native file picker where supported, or a download otherwise.

## Supported file formats

| File | Format |
|---|---|
| DBC | Windows-1252, standard syntax (`BO_`, `SG_`, `VAL_`, `CM_`, simple multiplexing) |
| Log | `.csv` (`Timestamp,CAN_ID,DLC,Data`) or `.asc` (Vector ASCII, multi-channel) |

Bit-level decoding (Intel/Motorola, signed values, multiplexing, multi-packet message truncation) was validated against [`cantools`](https://github.com/cantools/cantools) before the first version shipped.

## Known limitations

- Doesn't read raw `.blf` binary files — Vector BLF uses compressed containers in a proprietary format. Convert to `.csv` or `.asc` first (for example with `python-can`).
- Built for desktop Chrome/Edge; not optimized for mobile.
