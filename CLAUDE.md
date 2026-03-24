# Tomrummet Lights - Project Context

## VIKTIGT: Arbetssätt
- Detta projekt handlar om QLC+ ljusstyrning via XML-filer (.qxw, .qxi)
- **Detta är INTE kod** (inte Python, Go, JavaScript etc). Du kan inte gissa syntax eller beteende.
- QLC+ XML-format är dåligt dokumenterat. **Sök info och verifiera** innan du gör ändringar.
- Om du inte vet hur något funkar i QLC+ - **säg det**. Sök i docs, källkod eller forum. Gissa inte.
- QLC+ 5 (qmlui) och QLC+ 4 är olika. Mycket dokumentation online gäller v4. Var noga med att skilja.
- Commit + push direkt efter varje ändring. Patriks filer, ingen annan jobbar med dem just nu.

## Programversion
- **QLC+ 5.2.0** - INTE version 4. Dubbelkolla alltid att info gäller v5.
- QLC+ 5 docs: https://docs.qlcplus.org/v5
- QLC+ 5 källkod (qmlui): https://github.com/mcallegari/qlcplus

## Filer vi jobbar med
- **Workspace:** `patrik/tr_light_x_touch v2.qxw` (den ENDA filen vi rör)
- **Input Profile:** `patrik/Behringer-X-Touch-Compact.qxi`
- Andra .qxw-filer är borttagna/irrelevanta

## Hardware Setup
- **MIDI Controller:** Behringer X-Touch Compact (USB)
- **DMX Interface:** FT232R USB UART
- **Lampor:**
  - 8x Alkalite Tracpod 81 (48-kanalläge, ren RGB per head, 8 heads/fixture, INGEN separat dimmer-kanal)
    - Bak (FixtureGroup 5 "trackpod bak"): Fixture 6,7,8,9 (DMX addr 28,76,124,172)
    - Sida (FixtureGroup 6 "trackpod sida"): Fixture 14,17,16,18 (DMX addr 240,384,336,432)
    - Alla (FixtureGroup 4 "trackpod"): Alla 8 fixtures
  - 2x American DJ Revo 3 (4ch Standard mode): Fixture 12,13 (DMX addr 220,230)
  - 1x Martin Atomic 3000: Fixture 4 (DMX addr 19)
  - 1x Generic Smoke: Fixture 3 (DMX addr 17)

## QLC+ Konfiguration

### Universe 1 (ID=0)
- Input: MIDI, X-TOUCH COMPACT
- Output: DMX USB
- Feedback: MIDI, X-TOUCH COMPACT (Note Velocity mode)
- **Passthrough: False** (annars läcker MIDI-input ut som DMX)
- **MIDISendNoteOff: false**

### Virtual Console Pages
1. **Live** (ID=0) - Huvudsida för live-körning
2. **Live - effects** (ID=1) - Tom, för framtida effekter
3. **Mood** (ID=2) - Tom, för framtida moods
4. **Revo III - lab** (ID=3) - Revo-kontroller, minst använd

### Live-sidan Layout (speglar X-Touch Compact fysisk layout)

**Rad 1 - Effekter (SoloFrame):** (MIDI ch 144-151)
| Knapp | MIDI | Funktion | Action | X-Touch Mode |
|-------|------|----------|--------|-------------|
| 1 K.Rider | 144 | knight rider (15) | Toggle | Toggle |
| 2 Sparkle | 145 | white sparkle (16) | Toggle | Toggle |
| 3 Fire | 146 | trackpod fire (17) | Toggle | Toggle |
| 4 Rainbow | 147 | rainbow (48) | Toggle | Toggle |
| 5 Slow Pulse | 148 | TP Slow Pulse (135) | Toggle | Toggle |
| 6 Blinder | 149 | trackpod blinder (18) | Toggle | Momentary |
| 7 Strobe | 150 | trackpod strobb 60ms (19) | Toggle | Momentary |
| 8 Smoke | 151 | smoke (3) | Toggle | Momentary |

**Rad 2 - Bak-färger (SoloFrame):** (MIDI ch 152-159)
Red, Blue, Green, Purple, Yellow, Cyan, White, Orange

**Rad 3 - Sido-färger (SoloFrame):** (MIDI ch 160-167)
S.Red, S.Blue, S.Green, S.Purple, S.Yellow, S.Cyan, S.White, S.Orange

**Faders:** (MIDI ch 1-9)
| Fader | MIDI | Funktion |
|-------|------|----------|
| 1 | 1 | T.RED (Level) |
| 2 | 2 | T.GREEN (Level) |
| 3 | 3 | T.BLUE (Level) |
| 4 | 4 | ATOMIC (Level) |
| 5 | 5 | S.DIM (Playback - Side Wash Full) |
| 6-8 | 6-8 | Lediga |
| 9 GM | 9 | Grand Master |

## MIDI / LED Feedback - LÖST
- **X-Touch knappar i Toggle-mode:** LED:erna sköts av X-Touch hårdvara
- **X-Touch knappar i Momentary-mode:** (Blinder, Strobe, Smoke) - LED:er via QLC+ feedback
- **Input Profile:** ExtraPress=True på alla button grid-knappar (skickar om feedback efter release)
- **Feedback velocity:** 4 = LED på, 6 = LED blinkar
- **Flash-action funkar INTE med RGBMatrix-funktioner** - använd Toggle istället
- X-Touch Global Channel: 2

## Kända olösta problem
- **Dimmer för sido/bak-tracpods:** Tracpod 81 i 48ch har ingen dimmer-kanal.
  Submaster fungerar bara inom samma Frame (frame-baserat i QLC+ 5 källkod).
  Playback-mode är legacy/deprecated i QLC+ 5, ersatt av "Adjust".
  Behöver research: kanske QLC+ Script eller annan approach för att skala aktiv färg.
- **S.DIM fadern:** Kör "Side Wash Full" (RGBMatrix, vit) - funkar inte som dimmer, tänder bara vitt.
