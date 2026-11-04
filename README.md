# Context-Rich Prosthesis Dataset: Figure and Table Reproduction

Python code that regenerates the data figures and tables of *Context-Rich Longitudinal Multi-Device Dataset of
Powered Lower-Limb Prostheses* from the public dataset on Dryad.

* **Dataset:** Dryad, DOI to be added on publication. Continuous 100 Hz recordings from four powered-prosthesis
  families (VU, OSL, HK, Fox); see the dataset README for every file and column.
* **Code:** this repository, MIT license. It reads only the extracted public dataset and never writes to it.

## Get the data

Download every file of the Dryad dataset into one folder and extract every ZIP archive **in that same folder**. The
archives store dataset-relative paths, so the result is:

```text
DATASET/
  metadata.csv  DATA_DICTIONARY.json  DATASET_CONTENT.json  records.jsonl  checksums.sha256
  recordings/<family>/<id>.csv        currents/<family>/<id>.csv        annotations/<family>/<id>_pvd.csv.gz
  timing_quarantine/VU/<id>.csv.gz    native_currents/VU/<id>.csv.gz
```

About 19 GB once extracted. `python reproduce.py --check-dataset --dataset DATASET` checks the layout.

## Install

Tested with Python 3.12.

```bash
python -m pip install -r requirements.txt
python -m unittest discover -s tests -v
python reproduce.py --list
```

Run the commands from inside this folder. `DATASET` and the output folders can be anywhere; output folders must be new
and outside the dataset.

## Reproduce everything

```bash
python reproduce_all.py --dataset DATASET --output-dir outputs/full_run --allow-mixed-participants --workers 4
```

This builds the shared cycle analysis, then every figure and table, and writes `batch_report.json` listing what was
generated, excluded or failed. The full run reads every recording, so it takes a while; `--analysis-dir` reuses an
existing shared analysis.

## Reproduce one figure

Cycle-based figures share one analysis. Build it once:

```bash
python -m common.cycles --dataset DATASET --output-dir outputs/shared_analysis --workers 4
python reproduce.py --figure gait_sensor_grid      --dataset DATASET --analysis-dir outputs/shared_analysis --output-dir outputs/gait_sensor_grid
python reproduce.py --figure currents_by_activity  --dataset DATASET --analysis-dir outputs/shared_analysis --output-dir outputs/currents_by_activity
python reproduce.py --figure controller_parameters --dataset DATASET --analysis-dir outputs/shared_analysis --output-dir outputs/controller_parameters
python reproduce.py --figure step_counts           --dataset DATASET --analysis-dir outputs/shared_analysis --output-dir outputs/step_counts
python reproduce.py --figure sensor_amplitude      --dataset DATASET --analysis-dir outputs/shared_analysis --output-dir outputs/sensor_amplitude
python reproduce.py --figure representative_sensor_traces --dataset DATASET --analysis-dir outputs/shared_analysis --allow-mixed-participants --output-dir outputs/representative_sensor_traces
```

Figures that need only metadata or state labels:

```bash
python reproduce.py --figure activity_distribution --dataset DATASET --output-dir outputs/activity_distribution --workers 4
python reproduce.py --figure subject_timeline      --dataset DATASET --output-dir outputs/subject_timeline
```

Tables:

```bash
python tables/tab_device_sessions.py     --dataset DATASET --output-dir outputs/table_device_sessions
python tables/tab_participant_visits.py  --dataset DATASET --output-dir outputs/table_participant_visits
python tables/tab_dataset_structure.py   --dataset DATASET --output-dir outputs/table_dataset_structure
python tables/reproduce_metadata_tables.py --dataset DATASET --output-dir outputs/metadata_tables
```

Every output folder contains PNG/SVG figures plus the numbers behind them (CSV/JSON) and the hashes of the code and
inputs that produced them.

## Figures

| Manuscript label | Script | Reproducible from the public dataset |
|---|---|---|
| `fig:user_to_time_leg` | `figures/fig_subject_timeline.py` | Yes (relative visit days) |
| `fig:pieActivity` | `figures/fig_activity_distribution.py` | Yes |
| `fig:SM_params_across_activity` | `figures/fig_controller_parameters.py` | Yes |
| `fig:current_all_legs_activities` | `figures/fig_currents_by_activity.py` | Yes |
| `fig:timedData` | `figures/fig_representative_sensor_traces.py` | Yes, with `--allow-mixed-participants` (see below) |
| `fig:StepCountForLeg` | `figures/fig_step_counts.py` | Yes |
| `fig:GaitSensors` | `figures/fig_gait_sensor_grid.py` | Yes |
| `fig:amplitudeAndMean` | `figures/fig_sensor_amplitude.py` | Yes |
| `fig:historyLegs` | `figures/fig_device_usage_over_time.py` | No: needs calendar aggregates, not released |
| `fig:montlyActivity` | `figures/fig_activity_over_time.py` | No: needs calendar aggregates, not released |
| `fig:relationships`, `fig:docai_fig` | — | Not data figures |

The two calendar figures need monthly/quarterly aggregates that would reveal calendar timing, so they are not part of
the public dataset. Their renderers are kept to document the method (`--aggregates-dir`; schema in
`common/calendar_figures.py`); `reproduce_all.py` reports them as missing inputs.

## Methods in brief

Full details: [methods and limits](docs/methods_and_limits.md) and [activity occupancy](docs/activity_distribution.md).

* **One shared cycle inventory.** `common/cycles.py` segments every verified 100 Hz recording by its state labels;
  all cycle-based figures read the same inventory. Segments never cross recording files. Each segment is resampled
  once to 100 points. Counts are accepted gait segments, not verified heel strikes.
* **Endpoints.** A configuration's endpoint states are the first and last states it uses regularly (at least 10 % of
  its anchor state's entries), so controllers with reduced state sets (VU GEN3) are not rejected.
* **Stair ascent** strides run from one 31 entry to the next and may cross up to 1.5 s of standing (1X) labels.
* **Walking.** Sensor means pool 2X and 7X segments; currents and step counts use 2X only.
* **Signals are used as released:** no recalibration, sign changes or gap filling. Values are in uncalibrated source
  units, so compare shapes across families, not magnitudes. Native-clock VU recordings are excluded from all timed
  analyses.
* **Representative traces.** No participant has three consecutive eligible walking cycles on all four families, so the
  figure fails by default instead of mixing participants. `--allow-mixed-participants` draws a labeled mixed-participant
  version.

## Layout

```text
reproduce.py / reproduce_all.py   single-figure and batch entry points
figure_registry.json              manuscript labels, scripts, inputs and status
figures/                          one script per manuscript figure
tables/                           table scripts
common/                           shared analysis, rendering, metadata and I/O
configs/                          palettes, activity definitions, porting provenance
docs/                             methods and limitations
tests/                            synthetic regression tests (no dataset needed)
validate_reproduction.py          consistency checks on a full-dataset run
```

## License

Code: [MIT](LICENSE). The dataset is published on Dryad under CC0.

## Citation

If you use the code or the dataset, please cite the paper and the Dryad dataset (references to be added on
publication).
