# Tapestry — User's Guide

A sample editor for macOS aimed at sample-pack work: trimming, looping,
convolution, IR creation, and noise-burst synthesis. This guide walks
through what's on screen and how to drive it. 

Throughout the document, the Mac command key shortcuts are shown (⌘X, for example). On Windows, the same commands 
use the Ctrl key intead. 

## Getting started

Open a file via **File → Open...** (⌘O), drag-and-drop onto the window,
or pass paths on the command line / use Finder's *Open With*. Supported
formats: WAV, AIFF, MP3. Mono and stereo files are supported; if a multichannel file (e.g. 5.1 surround) the app 
warns that the clip will be converted to stereo and data will be lost if it's then saved. 

Layout, top to bottom:

- **Sidebar** (left) — shows thumbnail views of each currently loaded clip along with their names and summary 
  information. Click on them to switch between clips; each clip remembers its own cursor, selection, zoom, and
  scroll position.
- **Waveform** (center) — the active clip. Stereo clips are shown as
  two stacked traces.
- **Toolbar** - transport and zoom controls.
- **Status / loop bar** (bottom) — shows information about the current active clip, the last operation performed, 
  and the tempo/bar settings.

Multiple clips can be loaded at once; cut/copy/paste works between them.

## Cursor and selection

- **Click** on the waveform to place the cursor.
- **Click-drag** to make a selection. 
- **Long-press near a either end of the selection** to grab and relocate
  it instead of starting a fresh selection. Grab radius is configurable
  in Settings.
- **Middle-click drag** or ⌘-drag to pan.
- **Shift+]** / **Shift+\[** extend the selection to the next / previous
  detected onset; **]** / **[** alone move the cursor to the onset.
- **⌘A** selects the whole clip and parks the cursor at the start of the clip.

Most edits and effects operate on the selection if there is one, or the
whole clip otherwise.

## Playback

- **Space** — play / pause from the cursor.
- **Stop** — return to the start.
- **Loop** toggle — when a selection exists, playback loops *within
  the selection* (or stops at its end if loop is off, with the cursor
  parked at the selection start). With no selection, looping plays the
  whole clip on repeat.
- **Go to Start** — cursor to start of the clip.

## Zoom and pan

- **⌘+** / **⌘−** zoom in / out, anchored on the selection center if
  one exists, otherwise the cursor.
- **Zoom to Fit** — fills the view with the whole clip.
- **Mouse wheel** zooms; scrollbar appears when zoomed in.

## Editing

Standard clipboard works between clips and across windows.

- **Cut / Copy / Paste Insert** — ⌘X / ⌘C / ⌘V.
- **Paste Mix** (⇧⌘V) — sums the clipboard into the destination
  instead of inserting.
- **Paste Insert Crossfade** (⌥⌘V) and **Paste Mix Crossfade**
  (⇧⌥⌘V) — as Paste and Paste mix, but applies an automatic crossfade at the join.
- **Paste to New** (⌘B) — opens a new clip from the clipboard.
- **Delete Selection** (⌫) — gap-closing delete.
- **Crop to Selection** (⇧⌘C) — keep only the selection, discard the rest.
- **Trim Silence** (⌘T) — remove silence at the head and tail of the clip.
- **Trim and Smooth** (⇧⌘T) — trim silence and apply a short fade in/out at the point of trim.
- **Insert Silence...** — inserts the specified period of silence at the cursor.
- **Insert Noise Burst...** — generated burst spliced at the cursor.
- **Fade In / Out** (⌘I/⌘U) in three flavors (exponential, linear (Shift), or half-cosine (⌥/Alt) — operate on 
  selection or whole clip.

Undo / Redo: ⌘Z / ⇧⌘Z. Each clip has its own undo stack with a
configurable memory budget (Settings).

## Loops and tempo

The status bar at the bottom carries the loop fields: **BPM**, and **Bars**. When a clip is loaded, Tapestry 
attempts to determine its tempo, either by metadata in the file or by analysis. The user can enter a different tempo 
at any point.

- **Extract Loop By Bars** (⌘K) — extracts a loop from the cursor
  position using the BPM and bar count, applying a crossfade to smoothly loop, creating a new clip. When no 
  selection is made, a region of the clip following the cursor, representing the specified number of bars at
  the specified tempo, is lightly shaded. This is the portion of the clip that will be extracted by this command.
- **Extract Selection as Loop** (⌘L) — uses the current selection bounds and
  applies the crossfade, creating a new clip.
- **Fold** (⌘F) — folds the clip in half, producing a loop where the
  end mirrors back into the start (useful for creating ambient textures).
- **Rotate to Cursor** (⌘R) — moves the start of the clip to the
  cursor and wraps the previous head onto the tail. 

To quickly create a loop when the tempo is known, set the tempo and bars fields, and click to place the cursor at 
the desired loop start. The **]** / **[** detect-onset keys can help position the cursor just before a drumbeat or 
other sharp attack. The lightly shaded region shows what 
will be extracted. Perform **Extract Loop By Bars** (⌘K).

To create a loop when the tempo is not known, drag a selection or use the **]** / **\[** and
 **Shift+]** / **Shift+\[** to place the selection endpoints, then perform  **Extract Selection as Loop** (⌘L). 

### Auto tempo detection

When a clip with no embedded tempo metadata is loaded, Tapestry runs spectral-flux
+ amplitude tempo detection on the whole clip and shows the result in
the BPM field with a confidence percentage indicator (e.g. *83%*) next to
it. Higher confidence means the detector is more sure; below ~30% the
result should be taken with a grain of salt.

- The percentage **disappears as soon as you edit the BPM manually** —
  your value is treated as authoritative.
- For short clips (under 30 seconds by default, configurable) where
  detection isn't confident, Tapestry assumes the clip is meant to
  loop as a power-of-2 number of beats and back-computes the BPM
  from the clip length so the value lands in the 80–160 BPM range.
- **Detect Tempo** (⌘Y) re-runs detection on the current selection,
  or the whole clip if there's no selection.
- For long clips, detection runs behind a progress dialog so it
  doesn't block the UI.
- Auto-detect-on-load can be turned off in Settings.

Tempos that come back doubled or halved compared to what you expect
are an inherent quirk of autocorrelation-based detection — humans
disagree about the same groove all the time. Just halve or double
the BPM field manually if its interpretation disagrees with yours.

## Effects (Process menu)

- **Normalize** (⌘N) — peak-normalize selection or whole clip to the
  target peak (Settings).
- **Normalize −6 dB** (⌘M) — same, but to −6 dBFS, half of full-scale amplitude.
- **Fade In / Out** — three curves: exponential (⌘I / ⌘U), linear
  (with Shift), half-cosine (with ⌥/Alt).
- **Extreme Stretch...** (⌘P) — Inspired by PaulStretch, this command stretches a clip to arbitrary lengths.
  Use the dialog to set the factor (or target seconds / frames / bars)
  and the window / overlap. Best for ambient / texture work. Runs on
  a background thread with a progress bar.
- **Convolve with IR...** — FFT convolution against an impulse
  response. The picker defaults to the IR library folder. Output
  replaces the selection (or whole clip) and extends by the IR's
  tail; the result is peak-matched to the input.
- **Loop Convolve with IR...** — same, but treats the input as a
  loop so the convolution tail wraps cleanly.

## Noise Burst Generator

Two ways in: **File → New Noise Burst...** (creates a fresh 48 kHz
stereo clip) or **Edit → Insert Noise Burst...** (generates at the
host clip's rate and splices at the cursor).

- **Mono / Stereo** — stereo mode lets the right channel have its own
  attack/decay curve.
- **Spectrum slope** — −24 dB/octave (very dark) to +6 dB/octave
  (very bright). Bins below 25 Hz are zeroed. The peak bin is
  anchored to the lowest active bin for negative slopes, Nyquist
  for positive.
- **Stereo Correlation** — controls how similar the noise in the two channels is.  
- **Attack / Decay** — controls the amplitude contour envelope of the noise burst.
- **Peak amplitude** scales the enveloped noise.
- **1-sample spike at start** — optional, with its own amplitude.
  Lets dry signal pass through when the burst is used as an IR.

## Working with IRs

- **Save as IR...** — picker defaulted to the IR library folder
  (`~/Library/Application Support/net.ragebear.Tapestry/IRs/` on Mac),
  created on demand. Use this whenever you've crafted something
  reusable.
- **Convolve with IR...** picks from the same folder by default.
- A noise burst with the spike-at-start toggle on makes a useful
  building block for IR design — the dry signal passes through and
  the noise tail provides the colored room/space.
- Multichannel IRs (>2 channels, e.g. 5.1) are silently downmixed to
  stereo on load. 

## Settings

Open via **Tapestry → Settings...** (⌘,). The window is modeless
— you can edit the active clip with it open. Changes apply when you
click OK.

- **Onset detection** — threshold, analysis window, max lookback,
  fallback offset, minimum skip distance, rise / silence ratios.
  These drive the [ / ] navigation, paste-to-onset alignment, and
  loop-extraction snapping.
- **Loop extraction** — default bars, default BPM, default crossfade
  ms, max crossfade as a fraction of loop length.
- **Tempo detection** — auto-detect on load (on/off), loop-fallback
  max length in seconds (the threshold below which low-confidence
  detections fall back to length-based BPM).
- **Normalize** — target peak (0..1), silence floor.
- **Trim silence** — silence threshold, smooth-fade duration.
- **Mouse** — drag start delay (how long you have to hold before a
  click promotes to a drag-selection) and cursor grab radius.
- **Undo** — per-clip memory budget (MB).

**Reset Defaults** restores everything in the dialog to factory
values; **Cancel** discards your edits.

## Keyboard shortcut cheat-sheet

### File
- ⇧⌘N — New Clip
- ⌘O — Open...
- ⌘W — Close Clip
- ⌘S / ⇧⌘S — Save / Save As...
- ⌘, — Settings
- ⌘Q — Quit

### Edit
- ⌘Z / ⇧⌘Z — Undo / Redo
- ⌘A — Select All
- ⌘X / ⌘C — Cut / Copy
- ⌘V — Paste Insert
- ⇧⌘V — Paste Mix
- ⌥⌘V / ⇧⌥⌘V — Paste Insert Crossfade / Paste Mix Crossfade
- ⌘B — Paste to New
- ⌫ — Delete Selection
- ⇧⌘C — Crop to Selection
- ⌘T / ⇧⌘T — Trim Silence / Trim and Smooth
- ] / [ — Next / Previous Onset
- ⇧] / ⇧[ — Extend Selection to Next / Previous Onset

### Process
- ⌘K — Extract Loop By Bars
- ⌘L — Extract Selection as Loop
- ⌘F — Fold
- ⌘N — Normalize
- ⌘M — Normalize −6 dB
- ⌘R — Rotate to Cursor
- ⌘Y — Detect Tempo
- ⌘I / ⇧⌘I / ⌥⌘I — Fade In (exponential / linear / half-cosine)
- ⌘U / ⇧⌘U / ⌥⌘U — Fade Out (exponential / linear / half-cosine)
- ⌘P — Extreme Stretch...

### View / Transport
- ⌘+ / ⌘− — Zoom In / Out
- Space — Play / Pause

## Tips

- Selections preserve direction — extending leftward from an anchor on
  the right behaves the way you'd expect for shrinking back, but the
  *Min*/*Max* used by edits is always the normalized range.
- Operations that take a while (Extreme Stretch, Convolve, tempo
  detection on long clips) run on a background thread and show a
  progress bar; the UI stays responsive.
- The IR library is just a folder — drop files into it manually,
  organize subfolders however you like, and the file pickers will
  see them.
- Tempo detection is not magic. If the clip has no clear pulse, the
  fallback gives you a length-based BPM that's at least musically
  consistent with the clip looping cleanly. Trust your ears.


## Acknowledgements

Tapestry is developed and maintained by Russell Borogove.
Contact russell@ragebear.net with feedback.

Tapestry is written in C# using the following libraries and frameworks:

* BASS audio library https://www.un4seen.com
* Mark Heath's NAudio https://github.com/naudio/NAudio/
* Avalonia https://avaloniaui.net
* MessageBox.Avalonia https://github.com/AvaloniaCommunity/MessageBox.Avalonia
* CommunityToolkit.Mvvm https://learn.microsoft.com/en-us/dotnet/communitytoolkit/mvvm/

Thanks to Jake D'Arc for early testing and feedback.

