# Three-Band Audio Preamplifier and Equalizer

Analog three-band audio preamplifier and equalizer, redesigned and simulated in LTspice. Independent bass, midrange, and treble filter stages with adjustable band levels, an inverting summing amplifier, EQ/bypass switching, and a master volume control — no DSP involved.

Built on top of an open-source reference design ([thiasotiks/Audio-Preamplifier-With-3-band-Equalizer](https://github.com/thiasotiks/Audio-Preamplifier-With-3-band-Equalizer)); this project redesigns the filter characteristics and extends the signal path to hit specific target corner frequencies, then verifies everything with LTspice AC simulations.

## Design targets

| Band     | Target        | Simulated     | Error |
|----------|---------------|---------------|-------|
| Bass     | 200 Hz        | 203 Hz        | 1.5%  |
| Mid (HP) | 300 Hz        | 306 Hz        | 2.0%  |
| Mid (LP) | 4 kHz         | 3.855 kHz     | 3.6%  |
| Treble   | 6 kHz         | 6.016 kHz     | 0.27% |

## Signal flow

```
Input → Buffer → {Bass, Mid, Treble filters} → Level pots → Summing amp → Output attenuation → EQ/Bypass selector → Master volume → Output
```

- **Input buffer** — TL072 voltage follower, isolates the source and feeds the bypass path.
- **Bass** — second-order multiple-feedback low-pass filter, ~200 Hz corner.
- **Midrange** — cascaded second-order Sallen-Key high-pass + low-pass, ~300 Hz–4 kHz passband.
- **Treble** — second-order Sallen-Key high-pass filter, ~6 kHz corner, followed by a unity-gain phase-inversion stage to stop it from partially cancelling the midrange band at the summing node.
- **Level controls** — parameterized 10 kΩ potentiometers per band.
- **Summing amplifier** — TL072 inverting summing stage, combines the three bands.
- **EQ/Bypass** — LTspice voltage-controlled switches select either the processed output or the buffered input.
- **Master volume** — 100 kΩ output potentiometer.

Full derivations, transfer functions, and component values are in [`Report.pdf`](./Report.pdf).

## Repo structure

```
.
├── LTSpice-Sim/     # LTspice schematics and simulation files
├── Figures/         # Block diagrams and simulation plots used in the report
└── Report.pdf       # Full writeup: theory, design, and simulation results
```

## Running the simulation

1. Install [LTspice](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html) (free, Windows/macOS).
2. Clone the repo and open the schematic(s) in `LTSpice-Sim/`.
3. Run the AC analysis to reproduce the frequency-response plots in `Figures/`.

## Tools

LTspice, TL072 op-amps.

## Reference

- thiasotiks, "Audio-Preamplifier-With-3-band-Equalizer," GitHub repository — original reference implementation this project builds on.
- Texas Instruments, TL072 Datasheet.
- J. Karki, "Active Low-Pass Filter Design," TI Application Report SLOA049D.
- A. S. Sedra and K. C. Smith, *Microelectronic Circuits*, 7th ed.
