# FibTac Evaluation Code

This repository contains the Jupyter notebooks used to reproduce the validation/test evaluation for the five experimental tasks reported in the manuscript.

The validation/test data, the flow-measure ground-truth spreadsheet, and the model checkpoints are not stored in this GitHub repository because of file size. They are provided separately through the project data folder:

**Data and checkpoint link:** https://drive.google.com/drive/folders/1-cormLomKk7tbj0fnoz9g1Mofc9Pc3Gs

---

## Repository structure

After cloning this repository and downloading/extracting the files from Google Drive, the local folder should look like this:

```text
FibTac_gt/
  README.md
  requirements.txt
  .gitignore

  notebooks/
    01_chess_evaluation.ipynb
    02_underwater_grasp_evaluation.ipynb
    03_liquid_evaluation.ipynb
    04_granularity_evaluation.ipynb
    05_flow_measure_regression_evaluation.ipynb

  FibTac_data/                       # not tracked by Git; download from Google Drive
    Chess_validation/
    Underwater_grasp_test/
    Liquid_test/
    Granularity_test/
    Flow_measure_test/
      Flow Measure Groundtruth.xlsx
      <angle>_<level>/
        <sample_or_instance>/
          *.jpg

    01_Chess_validation.7z
    02_Underwater_grasp_test.7z
    03_Liquid_test.7z
    04_Granularity_test.7z
    05_Flow_measure_test.7z

  checkpoints/                       # not tracked by Git; download from Google Drive
    crnn_chess_weights.pth
    crnn_watergrasp_weights.pth
    s3d_liquid.pth
    s3d_granularity_weights.pth
    crnn_flow_measure_weights.pth

  results/                           # generated outputs; not tracked by Git
```

The `FibTac_data/`, `checkpoints/`, and `results/` folders are excluded from Git tracking. They are kept in the repository only as placeholder folders with README files.

---

## Downloading and extracting data

Download the five `.7z` archives from the Google Drive folder. Extract them so that the following folders appear directly inside `FibTac_data/`:

```text
FibTac_data/
  Chess_validation/
  Underwater_grasp_test/
  Liquid_test/
  Granularity_test/
  Flow_measure_test/
```

The archives should not create an extra nested level such as:

```text
FibTac_data/FibTac_data/Chess_validation/
FibTac_data/Chess_validation/Chess_validation/
FibTac_data/Flow_measure_test/test/
```

Using 7-Zip on Windows, extract each archive into the `FibTac_data/` folder. From a terminal, the equivalent commands are:

```bash
7z x 01_Chess_validation.7z -oFibTac_data
7z x 02_Underwater_grasp_test.7z -oFibTac_data
7z x 03_Liquid_test.7z -oFibTac_data
7z x 04_Granularity_test.7z -oFibTac_data
7z x 05_Flow_measure_test.7z -oFibTac_data
```

Download the model checkpoint files from Google Drive and place them directly inside `checkpoints/`:

```text
checkpoints/
  crnn_chess_weights.pth
  crnn_watergrasp_weights.pth
  s3d_liquid.pth
  s3d_granularity_weights.pth
  crnn_flow_measure_weights.pth
```

---

## Data layout expected by the notebooks

### Classification tasks

The four classification tasks use frame folders. Each task folder should contain class folders, and each class folder should contain sample/instance folders with `.jpg` frames:

```text
FibTac_data/<Task_folder>/
  <class_name_1>/
    <sample_or_instance_001>/
      000001.jpg
      000002.jpg
      ...
    <sample_or_instance_002>/
      000001.jpg
      ...
  <class_name_2>/
    <sample_or_instance_001>/
      000001.jpg
      ...
```

Class labels are inferred from sorted class-folder names. Do not rename the class folders unless the corresponding notebook is updated and the label order is checked.

### Flow-measure regression task

The flow-measure task has an additional spreadsheet for the flow-speed ground truth. The expected layout is:

```text
FibTac_data/Flow_measure_test/
  Flow Measure Groundtruth.xlsx
  <angle>_<level>/
    <sample_or_instance_001>/
      000001.jpg
      000002.jpg
      ...
    <sample_or_instance_002>/
      000001.jpg
      ...
```

For example, a folder named `2_8` is interpreted as:

```text
angle = 2
level = 8
```

The notebook looks up the corresponding flow speed from `Flow Measure Groundtruth.xlsx` using `(level, angle)`. The flow-measure folder should **not** contain an extra `test/` subfolder in this release.

---

## Notebook-to-file mapping

| Notebook | Task | Model | Dataset path | Checkpoint path | Main outputs |
|---|---|---|---|---|---|
| `01_chess_evaluation.ipynb` | Chess classification | CRNN | `FibTac_data/Chess_validation/` | `checkpoints/crnn_chess_weights.pth` | predictions, accuracy, confusion matrix |
| `02_underwater_grasp_evaluation.ipynb` | Underwater grasp classification | CRNN | `FibTac_data/Underwater_grasp_test/` | `checkpoints/crnn_watergrasp_weights.pth` | predictions, accuracy, confusion matrix |
| `03_liquid_evaluation.ipynb` | Liquid classification | S3D | `FibTac_data/Liquid_test/` | `checkpoints/s3d_liquid.pth` | predictions, accuracy, confusion matrix |
| `04_granularity_evaluation.ipynb` | Granularity classification | S3D | `FibTac_data/Granularity_test/` | `checkpoints/s3d_granularity_weights.pth` | predictions, accuracy, confusion matrix |
| `05_flow_measure_regression_evaluation.ipynb` | Flow-measure regression | CRNN regressor | `FibTac_data/Flow_measure_test/` + `Flow Measure Groundtruth.xlsx` | `checkpoints/crnn_flow_measure_weights.pth` | predictions, MAE/RMSE/R², diagnostic figures |

---

## Path configuration in the notebooks

Each notebook contains a configuration cell near the top. The key path variables are:

```python
# 01_chess_evaluation.ipynb
DATA_ROOT = PROJECT_ROOT / "FibTac_data" / "Chess_validation"
CHECKPOINT_PATH = PROJECT_ROOT / "checkpoints" / "crnn_chess_weights.pth"
OUTPUT_DIR = PROJECT_ROOT / "results" / "chess"

# 02_underwater_grasp_evaluation.ipynb
DATA_ROOT = PROJECT_ROOT / "FibTac_data" / "Underwater_grasp_test"
CHECKPOINT_PATH = PROJECT_ROOT / "checkpoints" / "crnn_watergrasp_weights.pth"
OUTPUT_DIR = PROJECT_ROOT / "results" / "underwater_grasp"

# 03_liquid_evaluation.ipynb
DATA_ROOT = PROJECT_ROOT / "FibTac_data" / "Liquid_test"
CHECKPOINT_PATH = PROJECT_ROOT / "checkpoints" / "s3d_liquid.pth"
OUTPUT_DIR = PROJECT_ROOT / "results" / "liquid"

# 04_granularity_evaluation.ipynb
DATA_ROOT = PROJECT_ROOT / "FibTac_data" / "Granularity_test"
CHECKPOINT_PATH = PROJECT_ROOT / "checkpoints" / "s3d_granularity_weights.pth"
OUTPUT_DIR = PROJECT_ROOT / "results" / "granularity"

# 05_flow_measure_regression_evaluation.ipynb
FLOW_ROOT = PROJECT_ROOT / "FibTac_data" / "Flow_measure_test"
DATA_ROOT = FLOW_ROOT
GROUNDTRUTH_XLSX = FLOW_ROOT / "Flow Measure Groundtruth.xlsx"
CHECKPOINT_PATH = PROJECT_ROOT / "checkpoints" / "crnn_flow_measure_weights.pth"
OUTPUT_DIR = PROJECT_ROOT / "results" / "flow_measure"
```

---

## Installation

Python 3.10 or later is recommended.

```bash
conda create -n fibtac python=3.10 -y
conda activate fibtac
pip install -r requirements.txt
python -m ipykernel install --user --name fibtac --display-name "Python (fibtac)"
```

For CUDA support, install the PyTorch and torchvision builds that match the local CUDA version, then install the remaining packages from `requirements.txt`.

---

## Running the notebooks

Start Jupyter from the repository root:

```bash
cd FibTac_gt
jupyter lab
```

Then open the notebooks in the `notebooks/` folder. Each notebook can be run independently after its dataset and checkpoint are placed in the expected folders.

The classification notebooks save a consistent set of files under `results/<task_name>/`:

```text
predictions.csv
metrics.json
confusion_matrix_counts.csv
confusion_matrix_percent.csv
confusion_matrix_percent.png
```

The flow-measure regression notebook saves:

```text
predictions.csv
metrics.json
metrics.csv
angle_prediction_scatter.png
angle_residual_histogram.png
speed_prediction_scatter.png
speed_residual_histogram.png
evaluation_results.pdf
```

---

## Quick path check

After extracting the data and placing the checkpoints, run this command from the repository root to check the expected files and folders:

```bash
python - <<'PY'
from pathlib import Path
import re
root = Path.cwd()
required = [
    root / "FibTac_data" / "Chess_validation",
    root / "FibTac_data" / "Underwater_grasp_test",
    root / "FibTac_data" / "Liquid_test",
    root / "FibTac_data" / "Granularity_test",
    root / "FibTac_data" / "Flow_measure_test",
    root / "FibTac_data" / "Flow_measure_test" / "Flow Measure Groundtruth.xlsx",
    root / "checkpoints" / "crnn_chess_weights.pth",
    root / "checkpoints" / "crnn_watergrasp_weights.pth",
    root / "checkpoints" / "s3d_liquid.pth",
    root / "checkpoints" / "s3d_granularity_weights.pth",
    root / "checkpoints" / "crnn_flow_measure_weights.pth",
]
for p in required:
    print(("OK      " if p.exists() else "MISSING ") + str(p))

flow_root = root / "FibTac_data" / "Flow_measure_test"
if flow_root.exists():
    condition_dirs = [p.name for p in flow_root.iterdir() if p.is_dir() and re.match(r"^\d+[_-]\d+$", p.name)]
    print(f"Flow condition folders found: {len(condition_dirs)}")
    if not condition_dirs:
        print("WARNING: no angle_level folders such as 2_8 were found directly under Flow_measure_test.")
PY
```

---

## Reproducibility notes

- The notebooks automatically use CUDA when available and fall back to CPU otherwise.
- Batch size is set to 1 by default because video samples may contain different numbers of frames.
- Random seeds are set in each notebook, but exact numerical results can still vary across GPU hardware, CUDA versions, PyTorch versions, and driver versions.
- The notebooks are intended for validation/test evaluation. Full training datasets are not included in this repository.
- To provide fully pinned software versions, run the following command in the verified environment and add the output as `requirements_frozen.txt`:

```bash
pip freeze > requirements_frozen.txt
```

---

## Code availability statement

A suitable code-availability statement for the manuscript is:

```text
The custom code and Jupyter notebooks used for data preprocessing, model evaluation, analysis, and figure generation in this study are publicly available at: <GitHub repository URL>. The repository includes evaluation notebooks for all five experimental tasks, configuration information, software dependencies, and the main parameters required to reproduce the analyses reported in this study. The validation/test datasets, model checkpoints, and the flow-measure ground-truth spreadsheet are available from the data repository described in the Data availability statement.
```
