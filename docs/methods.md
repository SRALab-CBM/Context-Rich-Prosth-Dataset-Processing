# Methods

## Gait cycles (`common/cycles.py`)

* Every 100 Hz recording is segmented by its `state_labels`. Segments never cross recording files. Native-clock VU
  files are skipped.
* Activities: walking 2X (and passive 7X), stair ascent 3X, stair descent 4X, ramp ascent 5X, ramp descent 6X.
* A segment starts at an entry into the configuration's first regularly used state and ends at the next one. A state
  counts as regularly used if it has at least 10 % of the anchor state's entries, so reduced state sets (VU GEN3) are
  kept.
* Stair ascent runs from one 31 entry to the next and may cross up to 1.5 s of standing (1X) labels.
* For four small ramp groups (Fox-0.2 5X, Fox-flip 6X, HK-1.1 5X, HK-1.2 6X), cycles are split at upward Fz threshold
  crossings.
* Segments longer than the 95th duration percentile of their configuration and activity are dropped.
* Each segment is resampled to 100 points. A signal is used only if valid for the whole segment; gaps are not filled.
* Counts are accepted gait segments, not verified heel strikes.

## Figures

* **Sensors and currents:** mean of eligible segments per configuration. Signals are used as released, in
  uncalibrated source units, so compare shapes across families, not magnitudes. Sensor walking pools 2X and 7X;
  currents and step counts use 2X only. All-zero ankle-current segments are left out of the ankle panel only.
* **Amplitude:** per-configuration median of each segment's peak-to-peak range and mean (at least 10 segments).
* **Controller parameters:** samples are averaged within each state run, then runs are averaged with equal weight.
  Error bars are standard deviations of run means. Similarity is Pearson r over common states; values from two states
  are always ±1.
* **Activity distribution:** share of recorded time in each state group (1X–9X), counting every 100 Hz sample.
  Unknown labels stay in the total.
* **Timeline:** days since each participant's first visit. No calendar dates.
* **Representative traces:** no participant has three consecutive walking cycles on all four families, so the
  figure needs `--allow-mixed-participants` and labels each family's participant.

## Limits

State labels come from the controllers and are not independent ground truth. Walking counts are lower than in early
drafts because standing (1X) is now labeled and splits walking bouts.
