# Three-Band Audio Preamplifier and Equalizer

Analog three-band audio preamp and EQ, redesigned and simulated entirely in LTspice — no DSP, no code, just op-amps and RC networks doing the work. Independent bass, midrange, and treble filters, adjustable band levels, an inverting summing stage, an EQ/bypass switch, and a master volume control.

This started as a group project built on top of [thiasotiks' open-source 3-band preamp design](https://github.com/thiasotiks/Audio-Preamplifier-With-3-band-Equalizer). I redesigned the filter stages from scratch to hit specific corner frequencies (200 Hz / 300 Hz–4 kHz / 6 kHz) and extended the signal path with new level controls, summing, and bypass logic, then verified all of it with LTspice AC sweeps.

## The interesting bug

When I first combined all three bands at the summing stage, the output level dipped in a way the math didn't predict. Turned out the treble and midrange filters were coming out of their respective op-amp stages with different phase relationships, so at the crossover region they were partially cancelling each other instead of adding. I fixed it by sticking a unity-gain inverting stage in the treble path, which flips the polarity, leaves the magnitude untouched, and the cancellation mostly disappears. This is documented properly (with the actual simulated waveforms) in the report, but it's the part of the project I'm most proud of, since it wasn't something I designed for up front — I found it in simulation and had to figure out why.

## Design targets vs. what I got

| Band     | Target    | Simulated  | Error |
|----------|-----------|------------|-------|
| Bass     | 200 Hz    | 203 Hz     | 1.5%  |
| Mid (HP) | 300 Hz    | 306 Hz     | 2.0%  |
| Mid (LP) | 4 kHz     | 3.855 kHz  | 3.6%  |
| Treble   | 6 kHz     | 6.016 kHz  | 0.27% |

All within a few percent of target, which for hand-picked E12-series resistor and capacitor values is about as tight as you can reasonably get without going to precision parts.

## Signal flow
Input → Buffer → {Bass, Mid, Treble filters} → Level pots → Summing amp → Output attenuation → EQ/Bypass selector → Master volume → Output


- **Input buffer** — TL072 voltage follower. Just isolates the source from the filter network's loading and also feeds the bypass path.
- **Bass** — second-order multiple-feedback low-pass, ~200 Hz corner.
- **Midrange** — a cascaded Sallen-Key high-pass and low-pass, giving a ~300 Hz–4 kHz passband.
- **Treble** — Sallen-Key high-pass, ~6 kHz corner, followed by the phase-inversion stage mentioned above.
- **Level controls** — parameterized 10 kΩ pots, one per band.
- **Summing amplifier** — TL072 inverting summing stage that combines all three.
- **EQ/Bypass** — implemented with LTspice voltage-controlled switches, toggles between the processed output and the raw buffered input.
- **Master volume** — 100 kΩ pot on the final output.

Full transfer function derivations and every component value are in [`Report.pdf`](./Report.pdf) — the README is the overview, the report is where the actual design work is shown.


## Running it yourself

1. Install [LTspice](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html) (it's free).
2. Clone this repo and open `Audio-Preamplifier.asc` from `LTSpice-Sim/`.
3. Run the AC analysis — you should get frequency response curves matching the ones in `Figures/`.

## Tools

LTspice, TL072 op-amps.

## References

- thiasotiks, "Audio-Preamplifier-With-3-band-Equalizer," GitHub repository — the reference design this builds on.
- Texas Instruments, TL072 Datasheet.
- J. Karki, "Active Low-Pass Filter Design," TI Application Report SLOA049D.
- A. S. Sedra and K. C. Smith, *Microelectronic Circuits*, 7th ed.
