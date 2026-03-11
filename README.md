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
| 18 | Generic Smoke | 2 | 3 | |
| 20 | Martin Atomic 3000 | 1 | 4 | |
| 29 | Tracpod TP-81 | 48 | 6 | 48ch, truss bak-kant |
| 77 | Tracpod TP-81 | 48 | 7 | 48ch, truss bak-kant |
| 125 | Tracpod TP-81 | 48 | 8 | 48ch, truss bak-kant |
| 173 | Tracpod TP-81 | 48 | 9 | 48ch, truss bak-kant |
| 221 | Revo III SR | 10 | 12 | 10ch mode, stage right |
| 231 | Revo III SL | 10 | 13 | 10ch mode, stage left |
| 241 | Tracpod VÄ-1 | 48 | 14 | 48ch, truss vänster kortsida |
| 289 | Generic RGB | 3 | 10 | Okänd/orphan, flyttad från 282 (överlapp med VÄ-1) |
| 297 | Eurolite LED PAR 56 RGB | 5 | 11 | 5ch, oanvänd i scener |
| 337 | Tracpod HÖ-1 | 48 | 16 | 48ch, truss höger kortsida |
| TBD | 6x Robe ParFect 100 | ? | — | Kommande |

## Principer

- `thomas/` rör vi aldrig
- Bygg i `patrik/`, kopiera till `live/` när det funkar
- Läs manualer innan vi gissar DMX-kanaler
- QLC+ testas på Windows/Lenovo (Mac-versionen är buggig)
- Tracpod har ingen dimmer, bara RGB per segment
