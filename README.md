# leo-tn-ntn-simulator
# TN + NTN mobility, in motion

An interactive, browser-based simulator of a hybrid 5G terrestrial (TN) and LEO non-terrestrial (NTN) network. It follows one user device (UE) through a single continuous story:

> starts on TN → TN degrades → a LEO satellite becomes available → TN→NTN handover → satellite pass → satellite→satellite handover → TN returns → NTN→TN handover

**Live demo:** http://hsafi.com/leo-tn-ntn-simulator/

It is a single self-contained HTML file (`index.html`): no build step, no dependencies, no server. Open it in any modern browser.

---

## What it shows

- **Scene:** two terrestrial macro sites, terrain and obstructions, and at least three LEO satellites crossing an orbit with a 10° elevation mask, coverage beams and the serving link.
- **Mobility logic:** a unified candidate table (TN cells and satellites scored together), explainable weighted network selection, hysteresis, time-to-trigger and ping-pong protection.
- **Handover state machine:** SEARCHING → ACQUIRING → CONNECTED → handover preparation / execution → CONNECTED on the new node, plus radio-link failure and recovery. Both break-before-make (BBM) and make-before-break (MBB) packet handling are shown.
- **Handover types:** TN→NTN, NTN→TN, Intra-NTN (satellite→satellite) and Intra-TN.
- **Terrestrial model:** n78 (3.5 GHz, 100 MHz, TDD) with selectable 4T4R, 8T8R, 32T32R and 64T64R antenna profiles and a MU-MIMO view. Capacity gain is computed from users, channel conditions, UE capability and interference, not applied as a fixed multiplier.
- **Dashboard:** telemetry cards with sparklines, comparison panel, event timeline (solid = past, faded = forecast), packet and session continuity, and a link-budget table.
- **Controls:** eight selectable scenarios, UE and service profiles, playback speed, and "event pacing" (slows playback around handovers so each phase is readable).

## How it works

Everything on screen derives from one model:

1. Geometry (orbit, range, elevation, Doppler, round-trip time)
2. Radio link budget for every TN cell and satellite
3. Candidate evaluation and policy score
4. Handover rules and state machine
5. Packet flow and application session
6. Telemetry and timeline

The model updates once per simulated second. Handover phases run on a millisecond sub-clock, which gives the interruption time. A background Web Worker runs a second copy of the model to produce the forecast part of the timeline.

---

## Sources, derived values and assumptions

Every parameter in the page carries one of four labels:

| Label | Meaning |
|---|---|
| **Confirmed** | Taken directly from a cited specification |
| **Simulated** | Derived by the model from confirmed or assumed inputs |
| **Assumed** | A modelling choice made for this demo; editable in the code |
| **Exploratory** | Illustrative; not validated |

**Confirmed (from specifications)**
- Satellite link: 3GPP TR 38.821 Set-1 parameters for LEO at 600 km, S-band (EIRP density 34 dBW/MHz), handheld UE noise figure 7 dB.
- Handover margin of 3 (TR 38.863) and time-to-trigger values (TS 38.331).
- Orbital geometry: standard spherical-Earth relations (R = 6371 km, h = 600 km).

**Simulated (derived)**
- Range, elevation, Doppler, delay, RSRP, SINR, throughput, dwell time, scores and handover timing.

**Assumed**
- Terrestrial path-loss and obstruction treatment, shadowing, and O2I loss values.
- MU-MIMO capacity model and antenna power, weight and efficiency figures.
- Policy thresholds (for example TN −115 dBm and NTN −127 dBm), scoring weights, ping-pong and dwell timers.
- BBM and MBB packet-handling timings.

**Exploratory**
- The optional "Dragonwing-style" TN architecture panel (RU, DU, L1, core) is illustrative only. It is not validated against any vendor's data, and no part of it runs on the satellites.

## Known simplifications

- Two scales are used in the scene: local terrain below, orbital plane above (altitude exaggerated for readability).
- Earth rotation is ignored.
- Satellite payload power is not modelled; the transparent-payload assumption is used.
- The urban scenario deliberately produces radio-link failures, because tall buildings cut the signal faster than a prepared handover can complete.
- Satellite signal levels from the 3GPP parameters (about −114 to −129 dBm) are lower than a terrestrial-style −115 dBm threshold would allow, which is why a separate NTN threshold is used.

---

## Using and editing

- **Run it:** open `index.html` in a browser, or use the live link above.
- **Change parameters:** all values are centralised in the `CFG` object near the top of the model code inside the HTML file.
- **Reduced motion and themes:** the page follows light/dark and reduced-motion system settings.

## Status

A demonstration for exploring mobility concepts. It is not a standards-compliant or vendor-validated network simulator, and the numbers should not be used for dimensioning or procurement decisions without independent verification.

## Licence

MIT. See `LICENSE` (add one if you publish for reuse).
