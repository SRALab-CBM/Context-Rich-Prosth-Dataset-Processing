# Context-Rich Prosthesis Dataset: Figure Reproduction

Regenerates the data figures and tables of *Context-Rich Longitudinal Multi-Device Dataset of Powered Lower-Limb
Prostheses* from the public Dryad dataset (DOI to be added on publication). The code only reads the dataset.

## Setup

1. Download the Dryad files into one folder and extract every ZIP there (about 19 GB):

   ```text
   DATASET/
     metadata.csv  DATA_DICTIONARY.json  records.jsonl  checksums.sha256
     recordings/<family>/<id>.csv   currents/<family>/<id>.csv   annotations/  timing_quarantine/  native_currents/
   ```

2. Install (Python 3.12) and check:

   ```bash
   python -m pip install -r requirements.txt
   python -m unittest discover -s tests
   python reproduce.py --check-dataset --dataset DATASET
   ```

## Usage

All figures and tables:

```bash
python reproduce_all.py --dataset DATASET --output-dir outputs/all --allow-mixed-participants --workers 4
```

One figure (cycle-based figures share one analysis, built once):

```bash
python -m common.cycles --dataset DATASET --output-dir outputs/analysis --workers 4
python reproduce.py --figure gait_sensor_grid --dataset DATASET --analysis-dir outputs/analysis --output-dir outputs/grid
```

`python reproduce.py --list` lists the figure names. Each output folder holds PNG/SVG files, the plotted numbers
(CSV/JSON) and hashes of the inputs and code. Output folders must be new.

| Figure | Script | Needs `--analysis-dir` |
|---|---|---|
| Participant timeline | `figures/fig_subject_timeline.py` | |
| Activity distribution | `figures/fig_activity_distribution.py` | |
| Controller parameters | `figures/fig_controller_parameters.py` | yes |
| Motor currents by activity | `figures/fig_currents_by_activity.py` | yes |
| Representative sensor traces | `figures/fig_representative_sensor_traces.py` | yes, plus `--allow-mixed-participants` |
| Step counts | `figures/fig_step_counts.py` | yes |
| Gait sensor grid | `figures/fig_gait_sensor_grid.py` | yes |
| Sensor amplitude | `figures/fig_sensor_amplitude.py` | yes |
| Device usage / monthly activity | `figures/fig_device_usage_over_time.py`, `figures/fig_activity_over_time.py` | not reproducible: need calendar data that is not public |

Tables: `python tables/<script>.py --dataset DATASET --output-dir outputs/<name>` for `tab_device_sessions`,
`tab_participant_visits`, `tab_dataset_structure` and `reproduce_metadata_tables`.

How cycles are segmented and averaged: [docs/methods.md](docs/methods.md).

## Layout

```text
reproduce.py, reproduce_all.py   entry points
figures/, tables/                one script per figure or table
common/                          shared cycle analysis, plotting, I/O
configs/, figure_registry.json   colors, activity definitions, figure list
tests/                           tests on synthetic data (no dataset needed)
```

## License

Code: [MIT](LICENSE). Dataset: CC0 on Dryad. Please cite the paper and the dataset (references to be added on
publication).
