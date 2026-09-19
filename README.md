# audio_connectors

KiCad 10 library of audio jacks whose footprints were drawn from the manufacturer drawing and
checked against the manufacturer's STEP model. Symbols in `audio_connectors.kicad_sym`, footprints in
`audio_connectors.pretty/`, 3D models in `3d/`. The Datasheet fields link to the manufacturer's (or LCSC's) PDF.

## How parts are chosen

Audio jacks all differ from each other in slight but incompatible ways, and the stock KiCad
`Connector_Audio` footprints rarely match a part that is actually in stock. So the first criterion is
documentation: a dimensioned manufacturer drawing plus a manufacturer STEP model, so the footprint can be
drawn from one and checked against the other. After that, stock and price. THT parts are picked from
Farnell's range; SMD parts from the JLCPCB catalogue, only those eligible for Economic PCBA.

Every footprint was verified by rendering it with its 3D model (`kicad-cli pcb render`) and checking that
the pins land in their holes/slots and the pegs in the NPTH.

## Using it in a project

The library goes into the project as a git submodule at **`lib/audio_connectors`** — that exact path,
because KiCad only resolves 3D-model paths relative to the open project (`${KIPRJMOD}`),
never relative to the library, so the footprints reference `${KIPRJMOD}/lib/audio_connectors/3d/…`.

```sh
git submodule add https://github.com/ardavast/audio_connectors.git lib/audio_connectors
```

Then add to the project's `fp-lib-table` and `sym-lib-table` (nickname `audio_connectors`):

```
(lib (name "audio_connectors")(type "KiCad")(uri "${KIPRJMOD}/lib/audio_connectors/audio_connectors.pretty")(options "")(descr ""))
(lib (name "audio_connectors")(type "KiCad")(uri "${KIPRJMOD}/lib/audio_connectors/audio_connectors.kicad_sym")(options "")(descr ""))
```

Do not add it to the global library tables. To edit the library, open it through a project that uses it
(the footprint editor then finds the 3D models); commit inside `lib/audio_connectors` and push. Opened on
its own, the `fp-lib-table`/`sym-lib-table` in this folder still make it browsable, but 3D previews are
missing because `${KIPRJMOD}` then points here.

Pads are named like the stock KiCad jacks: T = tip, R = ring, S = sleeve, TN / RN = break contact of T / R
(closed with no plug inserted, opens when a plug goes in; leave unconnected if not needed). Footprint origin
is the body front face on the plug axis; the plug enters from -X.

## 3.5 mm stereo (TRS), horizontal, THT

| Part | Symbol / footprint | Pins | Notes | Farnell |
|---|---|---|---|---|
| Kycon STX-3000 | `Kycon_STX-3000` / `Jack_3.5mm_Kycon_STX-3000_Horizontal` | 3: T, R, S | Plain TRS, all-plastic body 14 x 7.2 x 5.4 mm, two locating pegs. | 2839860 |
| Lumberg 1503 09 | `Lumberg_1503_09` / `Jack_3.5mm_Lumberg_1503_09_Horizontal` | 5: T, TN, R, RN, S | TRS with break contacts on T and R. Ø6 threaded bushing with knurled nut for panel mounting. | 1243244 |
| Same Sky SJ1-3533NG | `SameSky_SJ1-3533NG` / `Jack_3.5mm_SameSky_SJ1-3533NG_Horizontal` | 3: T, R, S | Plain TRS, 12.5 mm tall, plug axis 7 mm above the PCB, plain Ø6 bushing. Footprint is the KiCad stock one, verified against the Same Sky sheet. Huge stock everywhere. | 4814342 |

## 3.5 mm vertical, panel mount through a threaded bushing (Eurorack style), THT

| Part | Symbol / footprint | Pins | Notes | Farnell |
|---|---|---|---|---|
| Lumberg 1502 03 | `Lumberg_1502_03` / `Jack_3.5mm_Lumberg_1502_03_Vertical` | 3: T, TN, S | Mono with tip break contact. M6 x 0.5 bushing with nut, 10 mm PCB-to-panel, 9 x 10.5 mm body. | 1270966 |
| Lumberg 1502 07 | `Lumberg_1502_07` / `Jack_3.5mm_Lumberg_1502_07_Vertical` | 3: T, R, S | Stereo, no switch. Plain Ø6 x 3 mm bushing, no nut: the panel must be held by other parts. 11.5 mm PCB-to-panel, 6 x 8 mm body. | 4218066 |
| Lumberg 1502 08 | `Lumberg_1502_08` / `Jack_3.5mm_Lumberg_1502_08_Vertical` | 3: T, R, S | Stereo, no switch. M6 x 0.5 bushing with knurled nut, 11.8 mm PCB-to-panel, round Ø8 body. | 4218067 |

## 3.5 mm SMD, JLCPCB-assembled (all "Extended" parts, Economic and Standard PCBA)

| Part | LCSC | Symbol / footprint | Pins | Notes |
|---|---|---|---|---|
| Shou Han PJ-313D | C668608 | `ShouHan_PJ-313D` / `Jack_3.5mm_ShouHan_PJ-313D_Horizontal` | T, R, S (each on both sides) | Plain TRS, horizontal, 14 x 6 x 5 mm. Footprint drawn from the Shou Han drawing; the JLC/EasyEDA footprint has the tip pad 0.3 mm off. |
| Shou Han PJ-327A 5JJ | C668605 | `ShouHan_PJ-327A` / `Jack_3.5mm_ShouHan_PJ-327A_Horizontal` | T, TN, R, RN, S | TRS with break contacts on T and R, horizontal, 14.2 x 6 x 4.6 mm. |
| Shou Han PJ-320D | C431535 | `ShouHan_PJ-320D` / `Jack_3.5mm_ShouHan_PJ-320D_Horizontal` | T, R1, R2, S | 4-pole TRRS, the ubiquitous cheap one. Footprint is KiCad's stock `Jack_3.5mm_PJ320D_Horizontal` (verified within 0.11 mm of the drawing) with the EasyEDA 3D model attached. |

3D models for these come from EasyEDA via `easyeda2kicad --3d` (manufacturer-supplied to LCSC), aligned to the pads by measuring the model's feet.
Not chosen: PJ-31060 (has a stock KiCad footprint but is "Standard Only" assembly), PJ-342 (Standard Only), PJ-327C-4A (JLC footprint disagrees with its datasheet by 2 mm on one pad), PJ-320B (3-pad TRS, only ~5k stock).

## Vendor pin numbers and model notes

| Part | Pad = vendor pin | Notes |
|---|---|---|
| Kycon STX-3000 | S=1, R=2, T=3 | Official Kycon STEP. |
| Lumberg 1503 09 | S=1, T=2, R=3, TN=4, RN=5 | Pin cross offsets taken from the STEP. |
| Lumberg 1502 03 | S=1, T=2, TN=3 | Slots are 0.6 x 1.6 mm (sheet recommends 0.5 x 1.5). |
| Lumberg 1502 07 | S=2, R=3, T=4 | **T/R assignment is inferred** from Lumberg's symbol style, not stated on the sheet: check with a meter before relying on it. Model converted from Lumberg's IGES (their STEP renders with artifacts). |
| Lumberg 1502 08 | S=1, T=2, R=3 | **T/R assignment is inferred** from Lumberg's numbering convention. Model is a cleaned re-export of Lumberg's STEP (the original is inch-scaled with the nut floating off the part). Pins at x = ±2.65 mm as measured on the model (sheet says ±2.5). |
| Same Sky SJ1-3533NG | as stock KiCad | Same Sky's own STEP; KiCad ships no model for this part. |
| Shou Han PJ-313D | — | Tip pad at 8.5 mm from the front face per the Shou Han drawing (EasyEDA has 8.2). |

## 3D model sources

| File | Source | Modified |
|---|---|---|
| `Kycon_STX-3000_rD3.STEP` | Kycon, `kycon.com/website/Extranet/3D/` | No. The body is modelled hollow and open at the rear; that is how Kycon's file comes. |
| `Lumberg_1502_03.step`, `Lumberg_1503_09.step` | Lumberg, download button on the product page (`downloads.lumberg.com/3dmodelle/`) | No |
| `Lumberg_1502_07.step` | Lumberg's IGES from the same download | Converted to STEP with CadQuery/OCP (Lumberg's own STEP renders with artifacts) |
| `Lumberg_1502_08.step` | Lumberg's STEP | Re-exported with CadQuery/OCP: scaled from inch to mm, centred on the plug axis, nut seated on the bushing |
| `SameSky_SJ1-3533NG.step` | Same Sky product page | No |
| `ShouHan_PJ-313D.step`, `ShouHan_PJ-327A.step`, `ShouHan_PJ-320D.step` | EasyEDA/LCSC via `easyeda2kicad --3d` (C668608, C668605, C431535) | No. EasyEDA names the C431535 model "PJ-320A"; it matches the PJ-320D pads. |
