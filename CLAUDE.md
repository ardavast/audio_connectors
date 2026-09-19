# audio_connectors — working methods

What the library contains, why each part was chosen and what is planned: see `README.md`. This file
only records the methods used to add a part. KiCad 10; naming follows the stock KiCad libraries and
the vendor's own wording.

## Adding a part
1. Pick parts with a dimensioned drawing **and** a manufacturer STEP model; stock comes second.
2. Check the stock `Connector_Audio` footprints first by comparing `(pad …)` lines against the drawing.
   If one matches, copy it into `audio_connectors.pretty/` under the library's naming; otherwise draw it.
3. Footprint conventions: pads named T / R / S / TN / RN (not vendor pin numbers); origin = body front
   face on the plug axis, plug enters from -X. Record the vendor pin number ↔ pad mapping in the README.
4. 3D model path is `${KIPRJMOD}/lib/audio_connectors/3d/<name>.step` (see README for why). Only models
   referenced by a footprint go in `3d/`. Datasheet field = vendor URL (check it returns a PDF); PDFs are
   not committed.
5. Verify with a `kicad-cli pcb render` of a board holding the footprint: pins must land in their
   holes/slots, pegs in the NPTH. Where drawing and STEP disagree, slice the STEP at the pin necks
   (CadQuery) and trust the measured positions.

## LCSC / JLCPCB parts
- Pull footprint / 3D from EasyEDA: `uv run --with easyeda2kicad easyeda2kicad --footprint --3d
  --lcsc_id=Cxxxx --output x.kicad_sym`. Treat the footprint as a reference to diff against the
  datasheet, not as truth (PJ-313D was 0.3 mm off, PJ-327C-4A 2 mm off).
- EasyEDA STEPs arrive with arbitrary origin/rotation: slice the model for feet/pegs with CadQuery and
  derive the `(model (offset …)(rotate …))` from that.
- Parts search: jlcsearch.tscircuit.com API (needs a browser User-Agent; `is_basic` / `is_preferred`).
- Assembly eligibility is only shown on `https://jlcpcb.com/partdetail/Cxxxx` ("PCBA Type: Economic
  and Standard" vs "Standard Only"); the page needs a real browser (headless Playwright works).

## Vendor 3D models
- Lumberg: `downloads.lumberg.com/3dmodelle/lumberg_<mpn with _>.stp.gz`.
- Kycon: `kycon.com/website/Extranet/3D/`.
- Broken vendor files (inch-scaled, floating nut, render artifacts) are fixed by re-exporting through
  CadQuery/OCP: scale to mm, centre on the plug axis, PCB plane at z=0, so offset/rotate stay 0.
- Farnell blocks curl and headless browsers; product pages have to be opened by hand.

## Local-only files
Survey notes, scrape data, raw EasyEDA footprints, unused models and all datasheet PDFs (`datasheets/`) are kept on disk
but git-ignored (see `.gitignore`). Do not add `jlcpcb_easyeda_footprints/` to any fp-lib-table.
