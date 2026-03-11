# Tomrummet Lights

QLC+ config files for Tomrummet stage lighting.

## Mappstruktur

| Mapp | Beskrivning |
|------|-------------|
| `thomas/` | Thomas baseline-fil (funkar live, rör ej) |
| `patrik/` | Patriks arbetsfiler |
| `manuals/` | Utrustnings-PDFer |

## DMX-adresser

| Adr | Fixture | Kanaler | QXW ID | Notering |
|-----|---------|---------|--------|----------|
| 1 | Stairville LED PAR56 | 5 | 0 | |
| 6 | Stairville LED PAR56 | 5 | 1 | |
| 11 | ADJ ProPar 56RGB | 7 | 2 | |
| 18 | Generic Smoke | 2 | 3 | |
| 20 | Martin Atomic 3000 | 1 | 4 | |
| 21 | Stairville LED Par56 MKII RGBW | 8 | 5 | Full mode |
| 29 | Tracpod TP-81 | 48 | 6 | 48ch mode |
| 77 | Tracpod TP-81 | 48 | 7 | 48ch mode |
| 125 | Tracpod TP-81 | 48 | 8 | 48ch mode |
| 173 | Tracpod TP-81 | 48 | 9 | 48ch mode |
| 221 | Revo III SR | 10 | 12 | 10ch mode, stage right |
| 231 | Revo III SL | 10 | 13 | 10ch mode, stage left |
| 241 | Tracpod TP-81 (ny) | 48 | 14 | 48ch, position TBD |
| 282 | Generic RGB | 3 | 10 | Okänd/orphan |
| 289 | Tracpod TP-81 (ny) | 48 | 15 | 48ch, position TBD |
| 297 | Eurolite LED PAR 56 RGB | 5 | 11 | 5ch, oanvänd i scener |
| 337 | Tracpod TP-81 (ny) | 48 | 16 | 48ch, position TBD |
| 385 | Tracpod TP-81 (ny) | 48 | 17 | 48ch, position TBD |
| TBD | 6x Robe ParFect 100 | ? | — | Kommande |

## Principer

- `thomas/` rör vi aldrig
- Bygg i `patrik/`, kopiera till `live/` när det funkar
- Läs manualer innan vi gissar DMX-kanaler
- QLC+ testas på Windows/Lenovo (Mac-versionen är buggig)
- Tracpod har ingen dimmer, bara RGB per segment
