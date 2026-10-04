# PowerBeats Layout Workshop

**English** | [日本語](README.ja.md)

A browser tool that turns your own MP3/WAV into a custom layout (beatmap) for **PowerBeatsVR** on PC and Quest.

The in-game generator only knows the BPM. This tool listens to the whole song first. It keeps notes out of silent intros and outros, puts more notes where the song gets louder and busier, and gives repeated sections (verse 1 and verse 2, for example) the same layout. It also has a **Boxercise mode** that builds the layout from real boxing combinations instead of random placement.

Everything runs in your browser. Your audio is never uploaded anywhere, and there is nothing to install.

![Screenshot](docs/screenshot.png)

## Quick start

1. Open `index.html` in a desktop browser (Chrome, Edge or Firefox). You can double-click the downloaded file, or use the GitHub Pages link if the repository has one.
2. **Round 1:** load the audio file you will play in the game.
3. **Round 2:** check the tempo. Press **Play** with the metronome click on. If the clicks drift, pick another BPM candidate, use **TAP**, or type the BPM.
4. **Round 3:** pick a difficulty and a mode, then adjust the sliders. The timeline updates as you change things.
5. **Round 4:** press **Save JSON**.
6. Copy the JSON into the game's `Layouts` folder **next to the audio file, with the same name** (only the extension differs).

| Platform | Layouts folder |
|---|---|
| PC (Steam) | `steamapps/common/PowerBeatsVR/PowerBeatsVR_Data/Layouts` |
| Quest (via SideQuest or similar) | `Android/data/com.FiveMindCreationsUGhaftungsbeschrnkt.PowerBeatsVR/files/Layouts` |

Example: `My Song.mp3` + `My Song.json`

> Use the exact audio file you put in the game. Two rips of the same song often start at slightly different times, and the layout's `offset` is measured from your file.

## Features

### Song analysis
- **Tempo detection** with several candidates, including double and half tempo. You can also tap along or type a BPM. Songs with a steady programmed beat are snapped to a whole-number BPM.
- **Beat alignment** (offset and bar start), with a click-track preview and a ±250 ms nudge.
- **Silence detection.** No notes go into silent intros, outros or breaks. The threshold is adjustable.
- **No-note ranges.** Drag across the timeline to keep any part of the song empty.
- **Energy curve.** Quiet parts get wider spacing, loud parts get denser patterns.
- **Repeat detection.** Sections with similar chords and timbre reuse the earlier layout, optionally mirrored.
- **Plays to the end.** The game stops the song once the last beat has passed, so the tool adds an empty beat about 1 s before the audio ends. Silent or blocked outros are no longer cut off.

### Standard mode
Uses the same pattern types and geometry as the official generator: Punches, Streams, Swings, Squats, Dodges and Archways, plus PowerBall ratio, two-handed notes and spikes.

- Every element has a 0–10 frequency slider. **0 removes it completely**, so you can make a punch-only layout with no ducking or dodging.
- The defaults match the official generator: everything at 5, and Beginner uses only Streams and Punches.
- Streams can be kept to the quiet parts of the song so choruses stay punchy.

### Boxercise mode
Places notes as boxing combinations (1 jab, 2 cross, 3 lead hook, 4 rear hook, 5 lead uppercut, 6 rear uppercut):

- **Combinations.** About 40 combinations such as `1-2`, `1-2-3-2`, `6-3-2`, `1-2-slip-2` and `duck-6-3`. Longer ones unlock at higher difficulties.
- **Hand order.** Hands alternate naturally. After a slip you always counter with the opposite hand.
- **Resets.** Each combination ends with a short reset. Combinations start on strong beats, and are sometimes repeated twice as a drill.
- **Note placement.** Hooks are horizontal swings and uppercuts are upward swings. Body shots sit at stomach height, slips use side walls and ducks use overhead walls.
- **Stance.** Orthodox, southpaw, or a switch every 16 bars.
- **Amount controls.** You can set the punch interval (auto, every beat or every 2 beats), the rest after each combination, and the combination length.
- **Combination counts.** The result panel shows how often each combination was used.

### Other
- Settings are remembered per difficulty, and a seed makes results reproducible.
- **Add to existing JSON** keeps the other difficulties of a layout you already have, as long as BPM and offset match.
- The interface is in English and Japanese. The language is detected automatically and can be switched at the top right.

## Tips

- **Tempo detection in metal and other dense genres.** Fast double-kick drums can push double or half tempo to the top of the list. Try the ×2 / ÷2 buttons or TAP, then confirm with the metronome.
- **Notes too early or too late.** Use the **Shift** slider in Round 2. If the click lands on beat 2 instead of beat 1, use **Bar start ±1 beat**.
- **Too many or too few notes.** In Standard mode, use **Density** under Advanced settings. In Boxercise mode, use the three **Note amount** controls.
- **Repeat copying goes too far or not far enough.** Change **Repeat matching strictness** under Advanced settings.

## Limitations

- One BPM per song (a limit of the layout format). Songs that change tempo will drift.
- Beat and tempo detection can fail on songs with free rhythm, rubato, or very quiet drums. Set the BPM by hand in those cases.
- Boxercise swing directions are based on the official layouts and have only been checked by a small number of players. Feedback is welcome.
- Only full-beat placement is used. Half-beat notes are not generated.

## How it works

1. The audio is decoded with the Web Audio API and resampled to 22 kHz mono.
2. A short-time Fourier transform produces onset envelopes (full band, low end and drums), frame loudness, chroma (pitch classes) and a coarse spectral envelope.
3. Tempo candidates come from autocorrelation of the onset envelopes. Each candidate is refined with a comb filter over the whole song, which also gives the beat phase. The downbeat comes from bass onsets and chord changes.
4. Per-beat loudness and onset strength make the energy curve. Bars are compared with a self-similarity matrix of centered chroma and timbre to find repeated sections.
5. A planner walks through the song bar by bar. It copies the layout of matched earlier sections and otherwise picks patterns, or combinations in Boxercise mode, weighted by your sliders and the local energy.

No libraries are used. It is a single HTML file.

The layout file format is documented in [docs/FORMAT.md](docs/FORMAT.md).

## Disclaimer

This is an unofficial fan-made tool. It is not affiliated with or endorsed by Five Mind Creations, the developer of PowerBeatsVR. Back up your `Layouts` folder before replacing files.

## License

See [LICENSE](LICENSE).
