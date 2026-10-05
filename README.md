# Claudio Sonar Soundpack

The control room of a deep-sea submarine, for [Claudio](https://claudio.click).
Every sound is synthesized from a text recipe; there are no recordings in
this pack.

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

- **The constant work is small and dry.** A depth-gauge tick, a single
  contact blip, a hatch thunk, a bubble. These are 30 to 180 ms long with no
  tail, because they play thousands of times a day.
- **The long echoing ping is saved for what matters.** Claude finishing its
  turn is one deep ping on the root with three darker echoes fading behind
  it. Session start, notifications and permission requests use the same
  ping.
- **Loading asks, success answers, errors contradict.** A shell command
  starts with a low thunk on D4 and succeeds with a clean blip an octave up.
  A read starts with one bubble and comes back as two. A fetch sends a hail
  blip and its echo; on success the hail is answered upward, on failure the
  echo comes back a semitone flat and muffled.
- **One alarm.** `error/critical.wav` is a two-whoop dive klaxon. Nothing
  else in the pack is harsh.

The pack is in D minor pentatonic (D F G A C) with D as home. Success sounds
land on D or climb to it. Errors use the notes outside the scale (Eb, Db,
Ab, Gb), sag in pitch, or beat against a detuned copy of themselves.

44 sounds answer for 132 sound keys. Each sound's intent is written next to
it as the `about` line in `source/recipe.json`.

- Every sound sits at -18 LUFS with true peaks under -1 dBTP, so one volume
  setting suits all of them.
- Loading sounds are under 1 s, success under 1.5 s, errors under 2 s. The
  longest sound is the 3.7 s session start.
- 48 kHz 16-bit WAV, the player's own format.

## Layout

```text
soundpack.json          built: sound key -> sounds/...wav
sounds/                 built: mastered WAVs
source/recipe.json      the pack: every sound and every mapping, as text
source/synth/           rendered from the recipe
source/soundpack.json   written with the render: sound key -> source/synth/...wav
```

Only `source/recipe.json` is written by hand. Everything else is a build
product; do not edit it, change the recipe and rebuild.

## Changing The Pack

Needs a `claudio` with the `soundpack synth`, `soundpack master` and
`soundpack audit` commands. `claudio soundpack synth --help` documents every
recipe setting.

1. Edit `source/recipe.json`. Update the sound's `about` line if what it is
   meant to be has changed.
2. Rebuild and check:

   ```bash
   rm -rf sounds soundpack.json source/synth source/soundpack.json
   claudio soundpack synth source/recipe.json --out source
   claudio soundpack master source/soundpack.json --out .
   claudio soundpack audit soundpack.json --strict
   claudio soundpack validate soundpack.json
   ```

3. Read the audit row for the sound you changed against its `about` line. A
   sound described as "two ticks then a tone" should show 3 in the `HIT`
   column. Quiet echoes under a ringing ping are not always counted as hits;
   look at a spectrogram to check those:

   ```bash
   ffmpeg -i sounds/synth/ping-home.wav \
     -lavfi "showspectrumpic=s=1000x300:scale=log:fscale=log" ping-home.png
   ```

4. If `master` reports a sound as `truncated`, it is longer than its
   category allows, echo included. Shorten it in the recipe and rebuild.

CI runs the same validate and audit on every push, and rebuilds the pack
from `source/soundpack.json` to check that the committed `sounds/` match.
