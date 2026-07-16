# PLT → MF4 Converter

Converts MotionSolve `.plt` result files directly into ASAM MDF 4.10
(`.mf4`) for AVL Drive evaluation — no HyperGraph/CSV step. Drag-and-drop
tkinter GUI, packaged as a single Windows `dist\PLT_to_MF4_Converter.exe`.

> Related repos: [csv-to-mf4-converter](https://github.com/George-F-2000/csv-to-mf4-converter)
> (the older CSV route), [mf4-viewer](https://github.com/George-F-2000/mf4-viewer)
> (inspect the results), and SimBuilder (the unified pipeline app, which
> embeds copies of this repo's extraction modules).

## Project layout

| File | Role |
|---|---|
| `plt_reader.py` | MotionSolve v9.1 ASCII `.plt` format parser (+ the `.nam` request-name companion). Fails loudly on unknown formats. |
| `avl_extract.py` | The AVL Drive signal layer: which request/component feeds each channel (`SIGNAL_MAP`, matched by request *name*), unit conversions (`CHANNEL_CONFIG` with source-unit cross-check), derived channels, and the estimated EM currents (Power Demand / pack voltage). |
| `converter.py` | Orchestrates: read → extract → write MF4. |
| `app.py` | tkinter GUI: drag `.plt` files onto the window (tkinterdnd2, falls back to buttons) or onto the exe; editable pack-voltage field. |

## Usage

Drop a `.plt` onto `dist\PLT_to_MF4_Converter.exe` (its `.nam` must sit
next to it — MotionView writes it at export). The `.mf4` lands next to the
`.plt`.

## Building

Uses the shared venv from `..\csv-to-mf4-app`:

```powershell
..\csv-to-mf4-app\.venv\Scripts\pyinstaller.exe --onefile --windowed `
  --noconfirm --name PLT_to_MF4_Converter --collect-all tkinterdnd2 `
  --icon assets\plttomf4.ico --add-data "assets\plttomf4.ico;." app.py
```

## Notes

- EM currents are **estimated** (the model has no current channel):
  I = motor Power Demand / pack voltage (380 V default, editable).
- Solver units assumed MMKGS (MotionSolve default); the extractor warns if
  the file disagrees.
