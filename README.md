# Machine Learning of Classifying Bicycle Rides

This repository contains data exploration, training of different types of models and comparative  evaluation of them. The overall objective is to establish a reliable generalized solution which is able to infer for the user whether the current bike lane is bumpy or not.

# Bicycle Lane Quality Classification — Group 19

This project uses smartphone sensor recordings to classify bicycle lanes as smooth or bumpy. 
It includes data preprocessing, feature extraction and selection, supervised and unsupervised models, and model comparison.

The main notebook is `group19.ipynb`.

## Project structure

```text
group19.ipynb       Main analysis notebook
data/              Input recordings
artifacts/         Generated windows, features and model results
figures/           Saved plots
README.md          Setup instructions and dataset description
```

## Setup

The main notebook’s saved metadata records Python 3.12.2. 
Package versions are pinned.

The notebook imports these third-party packages:

- NumPy
- pandas
- Matplotlib
- SciPy
- scikit-learn
- Pillow
- scikit-fuzzy
- pyFUME

Jupyter and `ipykernel` are also needed to run the notebook.


### Run in VS Code

1. Open the project folder in VS Code.
2. Install the Python and Jupyter extensions if needed.
3. Open `group19.ipynb`.
4. Select the `.venv` Python environment as the notebook kernel.
5. Make sure the working directory is the project root.
6. Restart the kernel and run the cells from top to bottom.
7. Save the notebook after execution.


The notebook uses relative paths such as `./data` and `./artifacts`. Running it from another directory can cause file-loading errors.

## Dataset

The dataset contains 26 recordings from three participants. Participants are identified as `P01`, `P02` and `P03`.

| Participant | Bumpy recordings | Smooth recordings | Total |
|------|---:|---:|---:|
| P01  | 6  | 4  | 10 |
| P02  | 4  | 4  | 8  |
| P03  | 4  | 4  | 8  |
|Total | 14 | 12 | 26 |

Each recording contains one labelled road-surface condition. The label is read from the recording folder name.

For example:

```text
P01_B03
```

means participant 01, bumpy surface, recording 03. S indicates a smooth surface.

### Recording folders

Recording folders must be placed directly inside `data/`:

```text
data/
├── P01_B01/
│   ├── Accelerometer.csv
│   ├── Gyroscope.csv
│   ├── Gravity.csv
│   ├── Metadata.csv
│   └── ...
├── P01_S01/
└── ...
```

The main sensor files contain `seconds_elapsed`, `x`, `y` and `z` columns.

| File                    | Contents                           |
|-------------------------|------------------------------------|
| `Accelerometer.csv`     | Linear acceleration                |
| `Gyroscope.csv`         | Angular velocity                   |
| `Gravity.csv`           | Gravity components                 |
| `Metadata.csv`          | Device and recording information   |
| `TotalAcceleration.csv` | Total acceleration, when available |
| Image files             | Optional recording-location photos |

When total acceleration is unavailable, the preprocessing code calculates it from linear acceleration and gravity.

## Processing and features

The current code:

1. Aligns sensor readings by timestamp.
2. Removes five seconds from each end of a recording.
3. Calculates vertical and horizontal acceleration using gravity.
4. Filters vertical acceleration between 3 and 20 Hz.
5. Creates 14-second windows with a 4-second step, assuming 100 Hz sampling.
6. Extracts 66 time-domain and frequency-domain features per window.

The saved feature table currently contains 324 windows: 166 bumpy and 158 smooth. These counts can change when the input data or preprocessing settings change.

Feature-selection experiments include variance filtering, correlation filtering, Fisher scores and mutual information.

## Models and evaluation

Supervised methods:

- Decision tree
- Random forest
- Logistic regression
- K-nearest neighbours

Unsupervised methods:

- K-means
- DBSCAN
- Hierarchical clustering
- Gustafson–Kessel clustering
- Gaussian mixture model

The notebook contains recording-grouped cross-validation, leave-one-participant-out evaluation and separate held-out recording experiments. Check each section’s split settings before comparing results.

## Generated outputs

The notebook saves:

- Window arrays and metadata in `artifacts/`.
- Extracted features and feature rankings in `artifacts/`.
- Model metrics and predictions in `artifacts/results/`.
- Plots in `figures/`.

The model-comparison section reads previously exported CSV files; it does not retrain models. Its inputs must use the same current windows and evaluation splits.

After changing data, preprocessing or models, regenerate the affected result exports before running the comparison. Some updated model sections may require an export step; older CSVs should not be treated as results from the current code.
