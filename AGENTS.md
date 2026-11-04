# Figure Reproduction Contract

- This directory must work as a standalone repository. Never import code from
  its parent workspace or require the manuscript, private raw archive, old pickle
  datasets, source calendar dates or a re-identification crosswalk.
- Use only the public continuous CSV schema. Access sensors/currents by column
  name, respect validity masks and preserve explicit missingness. Do not invent
  physical units, calibration coefficients or missing moment measurements.
- Current columns already contain the accepted current-only polarity convention.
  Do not invert them again or apply their signs to the eight sensor channels.
- Native-clock exceptions are not certified 100 Hz data. Do not manufacture
  durations or mix them into verified-clock hours.
- Participants are identified by TF pseudonyms. Sessions and visits are
  neutral identifiers, not chronological counters. Use visit_number and
  elapsed_visit_days. Never reconstruct or publish participant-linked dates.
- Calendar plots require separate aggregate inputs that are not part of the
  public dataset. Render inputs without an approval record only with explicit
  --allow-review-only; never add a date crosswalk or its preparation tool here.
  Clinical images/overview diagram and curated hardware/demographic tables are
  not reproduced.
- Build one versioned shared cycle inventory before porting cycle-based figures.
  Segment within each original recording and express duration rules in seconds.
  Normalize each retained full cycle once, not individual states then concatenate.
  Log accepted/rejected boundaries and signal-specific exclusions. Sensor/current
  grids, amplitudes and heatmaps must consume the same inventory.
- Mean-only plots, family colors/shades, family-specific zero-aligned axes and
  PNG/SVG output belong in shared code. Scientific selections/weighting must be
  exported separately from the visual layout.
- Activity distribution is implemented using pooled decoded-state occupancy,
  integer-millisecond support and a clipped terminal bin. Do not regress to
  sparse-event counts, remove unknowns silently, or infer hours for native files.
- All ten data-driven entry points are implemented. Cycle-based plots require the
  shared analysis; two calendar figures require separate aggregates. Metadata-only
  tables are implemented. A representative plot must not silently mix participants;
  --allow-mixed-participants explicitly enables the documented alternative.
- Do not call every inventory segment a verified heel-strike stride. The legacy
  endpoint-state/fallback policy retains some group/file-edge fragments; log these
  and distinguish channel-specific contributing counts. The original PVD row-ratio
  conversion for targeted Fz durations is approximate, not a measured clock.
- Do not overwrite the dataset, existing outputs or manuscript. Outputs belong
  outside this code tree or under the ignored outputs directory.
- Code is MIT-licensed (LICENSE); the dataset is CC0 on Dryad. Do not add data,
  outputs or private inputs to this repository.
