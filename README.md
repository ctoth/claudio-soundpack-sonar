# Claudio Sonar Soundpack

The control room of a deep-sea submarine, for [Claudio](https://claudio.click),
built from real recordings: a struck singing bowl for the sonar ping, a
hydrophone under the Svalbard ice, a ship's bell, Morse code and a WWII dive
klaxon.

## Install

```bash
claudio soundpack add gh:ctoth/claudio-soundpack-sonar --name sonar --default
```

Already installed? Pull the latest:

```bash
claudio soundpack update sonar
```

## What It Sounds Like

Everything is heard through water: lowpassed, with nothing bright or
sustained at the top.

- **The constant work is small and dry.** A depth-gauge tick (a metal tap),
  a console blip, a hatch thunk, a bubble. These are 40 to 190 ms long with
  no tail, because they play thousands of times a day.
- **The long echoing ping is saved for what matters.** Claude finishing its
  turn is one deep ping, a singing bowl struck and tuned to the root, with
  three darker echoes fading behind it. Session start and permission
  requests use the same bowl. A notification is two strokes of a ship's
  bell.
- **Loading asks, success answers, errors contradict.** A shell command
  starts with a low thunk on D4 and succeeds with a clean blip an octave up.
  A read starts with one bubble and comes back as two. A fetch sends a Morse
  dit and its echo; on success the dit is answered upward, on failure the
  echo comes back a semitone flat and muffled.
- **One alarm.** `error/critical.wav` is two aoogahs from a real WWII
  submarine dive klaxon. Nothing else in the pack is harsh.
- **Real water.** Session start and compaction sit on a hydrophone
  recording from Svalbard, seal whistle included.

The pack is in D minor pentatonic (D F G A C) with D as home. Each recording
is repitched into that scale. Success sounds land on D or climb to it.
Errors use the notes outside the scale (Eb, Db, Ab, Gb, E) or beat against a
copy of themselves a semitone flat.

44 sounds answer for 132 sound keys. 41 of them are built on a recording; the
other 3 are synthesized. Each sound's intent is written next to it as the
`about` line in `source/recipe.json`.

- Every sound sits at -18 LUFS with true peaks under -1 dBTP, so one volume
  setting suits all of them.
- Loading sounds are under 1 s, success under 1.5 s, errors under 2 s. The
  longest sound is the 3.7 s session start.
- 48 kHz 16-bit WAV, the player's own format.

## Layout

```text
soundpack.json          built: sound key -> sounds/...wav
sounds/                 built: mastered WAVs
source/recipe.json      the pack: every sound, every slice and every mapping, as text
source/raw/             the recordings the recipe slices, unedited
source/sources.json     author, source and licence of every file in source/raw/
source/synth/           rendered from the recipe
source/soundpack.json   written with the render: sound key -> source/synth/...wav
```

Only `source/recipe.json` and `source/sources.json` are written by hand.
The recordings in `source/raw/` are never edited: every cut, repitch and
filter is a line in the recipe. Everything else is a build product; do not
edit it, change the recipe and rebuild.

## Changing The Pack

Needs a `claudio` with the `soundpack synth`, `soundpack master` and
`soundpack audit` commands, and a `synth` that supports `sample` layers.
`claudio soundpack synth --help` documents every recipe setting.

1. Edit `source/recipe.json`. Update the sound's `about` line if what it is
   meant to be has changed. To retune a recording, set `speed` to the target
   frequency divided by the recording's own (the singing bowl is 661 Hz, the
   Morse tone 800 Hz, the bell's hum 732 Hz).
2. If you add a recording, put it under `source/raw/<collection>/` and add
   its author, source and licence to `source/sources.json`. Do not add a
   file whose licence you cannot establish.
3. Rebuild and check:

   ```bash
   rm -rf sounds soundpack.json source/synth source/soundpack.json
   claudio soundpack synth source/recipe.json --out source
   claudio soundpack master source/soundpack.json --out .
   claudio soundpack audit soundpack.json --strict
   claudio soundpack validate soundpack.json
   ```

4. Read the audit row for the sound you changed against its `about` line. A
   sound described as "two ticks then a toll" should show 3 in the `HIT`
   column. Quiet echoes are not always counted as hits; look at a
   spectrogram to check those:

   ```bash
   ffmpeg -i sounds/synth/ping-home.wav \
     -lavfi "showspectrumpic=s=1000x300:scale=log:fscale=log" ping-home.png
   ```

5. If `master` reports a sound as `truncated`, it is longer than its
   category allows, echo included. Shorten it in the recipe and rebuild.

CI runs the same validate and audit on every push, and rebuilds the pack
from `source/soundpack.json` to check that the committed `sounds/` match.

## Sources

Every recording under `source/raw/` is listed in `source/sources.json` with
its author, source page and licence. All of them are slices that have been
repitched, filtered and layered; none is used unmodified.

Recordings from Wikimedia Commons:

| Recording | Author | Licence | Used for |
| --- | --- | --- | --- |
| [The sound of a singing bowl](https://commons.wikimedia.org/wiki/File:The_sound_of_a_singing_bowl.wav) | Valera N. Trubin | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0) | every sonar ping and its echoes |
| [WWS ShipSodekshipsbell](https://commons.wikimedia.org/wiki/File:WWS_ShipSodekshipsbell.ogg) | Work With Sounds / Museum of Municipal Engineering | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0) | the ship's bell (notification, celebration) |
| [Svalbard Sample for Wiki](https://commons.wikimedia.org/wiki/File:Svalbard_Sample_for_Wiki.ogg) | Max Eastley | Public domain | hydrophone water (session start, compaction, search) |
| [WWII submarine dive klaxon](https://commons.wikimedia.org/wiki/File:WWII_submarine_dive_klaxon.ogg) | United States Navy | Public domain | the critical-error klaxon |
| [Morse Code - Slash](https://commons.wikimedia.org/wiki/File:Morse_Code_-_Slash.ogg) | JoeDeRose | Public domain | network hails, the sent-message signal |
| [Gong or bell vibrant (short)](https://commons.wikimedia.org/wiki/File:Gong_or_bell_vibrant_(short).ogg) | PDSounds | [CC0](http://creativecommons.org/publicdomain/zero/1.0/deed.en) | the low hull toll |

The two CC BY 4.0 recordings require this credit: the singing bowl is by
Valera N. Trubin and the ship's bell is by Work With Sounds / Museum of
Municipal Engineering; both are modified here (cut, slowed, filtered and
layered).

Sounds by [Kenney](https://kenney.nl), all [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/):

| Files | Kenney pack | Used for |
| --- | --- | --- |
| `drop_002`, `drop_003`, `drop_004`, `bong_001`, `question_004` | [Interface Sounds](https://kenney.nl/assets/interface-sounds) | console blips, bubbles, the hatch thunk, contact tones |
| `impactMetal_medium_004` | [Impact Sounds](https://kenney.nl/assets/impact-sounds) | depth-gauge ticks |
| `creak1` | [RPG Audio](https://kenney.nl/assets/rpg-audio) | the hull creak, slowed to a quarter speed |
| `engineCircular_002` | [Sci-Fi Sounds](https://kenney.nl/assets/sci-fi-sounds) | the turbine |
