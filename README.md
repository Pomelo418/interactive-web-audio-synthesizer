# Interactive Web Audio Synthesizer

A polyphonic synthesizer that runs entirely in the browser — no dependencies, no build step, one HTML file.

Built with the [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) and vanilla JavaScript.

## Features

- **Piano keyboard** — two playable octaves, shift up/down without limits
- **Four waveforms** — sine, square, sawtooth, triangle
- **ADSR envelope** — attack, decay, sustain, release with a live canvas visualizer
- **Feedback delay** — toggle on/off with adjustable time, feedback, and wet/dry mix
- **Master volume** control
- **Polyphonic** — hold multiple notes simultaneously
- **Mouse, touch, and PC keyboard** input

## Usage

Open `index.html` in any modern browser. No server required.

### PC Keyboard Map

```
White keys:  A  S  D  F  G  H  J  K  L
Black keys:  W  E     T  Y  U     O  P
```

| Key | Action |
|-----|--------|
| `A` – `L` | White keys (C through E, next octave) |
| `W E T Y U O P` | Black keys (C# D# F# G# A#) |
| `Z` | Octave down |
| `X` | Octave up |

### Controls

| Control | Description |
|---------|-------------|
| **Oscillator** | Select the waveform shape — affects the timbre (tone color) of every note |
| **Attack** | Time for the note to ramp from silence to full volume |
| **Decay** | Time to fall from peak to the sustain level |
| **Sustain** | Volume level held while the key is held down |
| **Release** | Time to fade to silence after the key is released |
| **Delay** | Feedback echo effect — toggle on/off, adjust time, feedback amount, and mix |
| **Volume** | Master output level |

## How It Works

### Audio Signal Graph

```
[Oscillator] → [Gain / ADSR] ─┐
                               ├→ [Master Gain] → [Dry path] ─────────────────┐
                               │                → [Delay Node] → [Feedback] ──┤
                               │                              → [Wet Gain] ───┤
                               │                                               ↓
                               │                                     [Audio Destination]
```

Each keypress creates a fresh oscillator and gain node pair. The gain node's automation curve is what produces the ADSR shape. On key release the gain ramps to zero (over the release time) before the oscillator is stopped, preventing click artifacts.

The delay is wired as a parallel wet/dry chain so the dry signal is never coloured by the effect routing.

### ADSR Scheduling

Envelopes are scheduled using `AudioParam` methods (`setValueAtTime`, `linearRampToValueAtTime`) on the audio thread — not `setTimeout` — so timing stays sample-accurate regardless of JavaScript thread load.

On release, `cancelScheduledValues` followed by an immediate `setValueAtTime` snapshot captures the current gain before starting the release ramp, preventing jumps when a key is released mid-attack or mid-decay.

## Browser Support

Any browser with Web Audio API support — all modern versions of Chrome, Firefox, Safari, and Edge.

## License

MIT
