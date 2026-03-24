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
  - 8x Alkalite Tracpod 81 (48-kanalläge, 8 heads/fixture)
    - **Kanaler per head (6st):** 1=Rainbow, 2=Red, 3=Green, 4=Blue, 5=Strobe, 6=Sound-active
    - **INGEN separat dimmer-kanal** i något mode (3ch, 6ch, 12ch, 24ch, 48ch)
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
- **Passthrough: False** (annars läcker MIDI-input ut som DMX - bekräftat felsökt)
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
| 4 | 4 | Ledig (planerad: B.DIM) |
| 5 | 5 | Ledig (planerad: S.DIM) |
| 6 | 6 | ATOMIC (Level) |
| 7-8 | 7-8 | Lediga |
| 9 GM | 9 | Grand Master |

## MIDI / LED Feedback - LÖST
- **X-Touch knappar i Toggle-mode:** LED:erna sköts av X-Touch hårdvara (satt i X-Touch Editor)
- **X-Touch knappar i Momentary-mode:** (Blinder, Strobe, Smoke) - LED:er via QLC+ feedback
- **Input Profile:** ExtraPress=True på alla button grid-knappar (skickar om feedback efter release)
- **Feedback velocity:** 4 = LED på, 6 = LED blinkar
- **Flash-action funkar INTE med RGBMatrix-funktioner** - använd Toggle istället
- X-Touch Global Channel: 2
- **MIDISendNoteOff: false** - annars tolkar QLC+ Note Off som ny toggle-event
- Referens: https://www.qlcplus.org/forum/viewtopic.php?t=10943

## Tracpod Dimmer - Research & Plan

### Problem
Tracpod 81 i 48ch-mode har ingen dimmer-kanal. Vi vill ha S.DIM och B.DIM faders
som skalar den aktiva färgen (RGB-värdena) proportionellt ner, precis som Grand Master
gör för allt.

### Utredda alternativ som INTE funkar
- **QLC+ Script:** Kan inte läsa aktiva kanalvärden (ingen `getfixture`), inga loopar, ingen matte
- **Playback/Adjust mode:** Legacy i QLC+ 5, styr en enda funktion, kan inte skala aktiv färg
- **Level slider:** Sätter absoluta värden, skalar inte
- **RGBMatrix ControlModeDimmer:** Kräver att fixture har dimmer-kanal (som Tracpod saknar)
- **Submaster:** Funkar tekniskt men kräver att alla widgets sitter i samma VCFrame.
  Förstör layouten som ska spegla X-Touch Compact. Möjlig nödlösning men ful.

### Plan: Forka QLC+ och lägga till Channel Group Master

Grand Master i QLC+ multiplicerar alla kanalvärden med en faktor (0.0-1.0) i
`Universe::updatePostGMValue()`. Vi gör samma sak men begränsat till utvalda kanaler.

**Klassen `ChannelsGroup` finns redan** i engine/src/channelsgroup.h/.cpp med:
- `m_masterValue` (uchar)
- `m_channels` (lista av fixture+kanal-par)
- Men den är BARA kopplad till Simple Desk, inte till DMX-outputen

**Ändring i Universe::updatePostGMValue()** (engine/src/universe.cpp rad ~557):
```cpp
void Universe::updatePostGMValue(int channel)
{
    uchar value = preGMValue(channel);
    if (value != 0)
        value = applyGM(channel, value);
    value = applyChannelGroupMaster(channel, value);  // NY RAD
    value = applyModifiers(channel, value);
    value = applyPassthrough(channel, value);
    (*m_postGMValues)[channel] = static_cast<char>(value);
}
```

**Scope:** ~100-200 rader C++, inget QML/UI-jobb. Config via XML i workspace-filen.
En VC-slider pekar på en ChannelGroupMaster istället för en funktion.

### DMX Output Pipeline (referens)
```
Function (Scene/Chaser/RGBMatrix)
  → GenericFader::write() (multiplicerar med intensity)
    → Universe::write() (HTP-check, lagrar i preGMValues)
      → updatePostGMValue()
        → applyGM()                    ← Grand Master
        → applyChannelGroupMaster()    ← NY: vår Channel Group Master
        → applyModifiers()
        → applyPassthrough()
        → postGMValues → dumpOutput() → DMX hardware
```

### Nyckelfiler att ändra
- `engine/src/universe.h/.cpp` - lägg till per-kanal skalfaktor + applyChannelGroupMaster()
- `engine/src/channelsgroup.h/.cpp` - koppla m_masterValue till Universe
- `engine/src/doc.h/.cpp` - ladda/spara ChannelGroupMaster från XML
- `qmlui/virtualconsole/vcslider.h/.cpp` - ny slider-mode "ChannelGroupMaster"

## Kända olösta problem
- **S.DIM / B.DIM faders:** Kräver Channel Group Master fork (se ovan)
- **S.DIM fadern:** Nuvarande "Side Wash Full" (RGBMatrix, vit) funkar inte som dimmer

## TODO
- [ ] Forka QLC+ och implementera Channel Group Master
- [ ] Fylla Live - effects sidan (page 2)
- [ ] Fylla Mood sidan (page 3)
- [ ] Eventuellt flytta Smoke till play-knappen
