# EMG Processing and Factor Analysis

A **Streamlit-based research application** for multichannel electromyography (EMG) preprocessing, factor analysis, feature engineering, classification, and comparative benchmarking.

The application provides an interactive workflow for inspecting EMG recordings, experimenting with signal-processing methods, analyzing latent components, and evaluating machine-learning classifiers.

> **Status:** Research-oriented software. Results should be validated against the acquisition protocol, ground truth, and experimental design before clinical or scientific interpretation.

## Features

### 1. EMG Signal Preprocessing

- **Data import:** Multiple CSV or MATLAB `.mat` recordings; interactive multichannel visualization.
- **Baseline correction:** Constant and linear detrending.
- **Filtering:** Band-pass, low-pass, high-pass, power-line notch, band-stop, and harmonic notch; Butterworth, Chebyshev Type II, and elliptic options where applicable.
- **Denoising:** Wavelet thresholding (multiple wavelet families), Savitzky–Golay, median filtering, Wiener filtering, PCA low-rank approximation, ICA component rejection, and empirical mode decomposition (EMD).
- **Spike artifact removal:** Hampel filter, local-median detection with interpolation, and derivative-threshold detection with interpolation.
- **Muscle activation:** Teager–Kaiser energy operator (TKEO).
- **Channel quality control:** Automatic screening and manual exclusion of corrupted channels.
- **Rectification and envelopes:** Full-wave and half-wave rectification, Hilbert envelope, moving RMS, moving-average envelope, and low-pass linear envelope.
- **Normalization:** Z-score, min–max, robust median/IQR, RMS, peak amplitude, and unit-energy scaling.
- **Segmentation:** Fixed windows and activity-based approaches.

### 2. Factor Analysis

Available methods include:

| Family | Methods |
| --- | --- |
| Single-view | Exploratory Factor Analysis (EFA), Independent Component Analysis (ICA), Nonnegative Matrix Factorization (NMF) |
| Two-view | Canonical Correlation Analysis (CCA), regularized CCA (rCCA), Partial Least Squares (PLS) variants |
| Multi-view | Multiset CCA (MCCA), Joint and Individual Variation Explained (JIVE), Common and Individual Factor Analysis (CIFA) |

The Factor Analysis tab supports factor scores and loadings, EMG reconstruction, reconstruction-quality metrics, synthetic demonstrations, **Build Multi-view Dataset**, and **Factor-analysis Reconstruction Comparison**.

### 3. Feature Engineering

Features can be extracted from preprocessed EMG or estimated factor signals.

| Domain | Example features |
| --- | --- |
| Time | Mean, standard deviation, extrema, skewness, kurtosis, RMS, energy, autocorrelation |
| Frequency | Peak and median frequency, spectral entropy, centroid, spread, skewness, kurtosis |
| Time–frequency (STFT) | Spectral-flux statistics |
| Wavelet (CWT) | Morlet-based energy, entropy, and coefficient statistics |

Feature selection and reduction options include variance and correlation screening, PCA, ANOVA, mutual information, mRMR, and information-theoretic/FEAST-style selection in the classification workflow.

### 4. Classification Stage

Train and evaluate models using features generated in the application or externally imported feature and label files.

**12 supported classifiers:**

- Linear Discriminant Analysis (LDA), Quadratic Discriminant Analysis (QDA)
- Logistic Regression, Ridge Classifier
- Support Vector Machine (SVM), k-Nearest Neighbors (k-NN)
- Gaussian Naive Bayes
- Decision Tree, Random Forest, Extra Trees, Gradient Boosting
- Multilayer Perceptron (MLP / Neural Network)

Outputs include held-out classification metrics, per-class statistics, confusion matrices, and ROC/precision–recall visualizations where supported.

The **Feature-engineering Method × Classifier Benchmark** is integrated into the Classification Stage and supports **stratified K-fold** and **leave-one-subject-out (LOSO)** evaluation using the prepared multi-view dataset.

### 5. Export

Download relevant results as CSV, including factor scores, loadings, reconstructed EMG, extracted features, feature rankings, classification metrics, and benchmark comparisons.

## Requirements

- Windows 10/11 or Ubuntu Linux
- Python **3.10 or newer** recommended
- `pip` and a Python virtual environment
- Modern web browser

The project's `requirements.txt` contains:

```text
streamlit>=1.36
numpy>=1.26
pandas>=2.0
scipy>=1.11
scikit-learn>=1.4
plotly>=5.20
PyWavelets
EMD-signal
```

> EMD uses the `PyEMD` module installed by the **`EMD-signal`** distribution. PyWavelets is required for wavelet denoising. The remaining algorithms are implemented using the listed dependencies and application code.

## Installation

### Windows (PowerShell)

Install Python from [python.org](https://www.python.org/downloads/) and enable **Add Python to PATH** during installation. Open PowerShell in the project directory:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
streamlit run app_v6_2.py
```

If PowerShell blocks environment activation, either use Command Prompt:

```bat
.venv\Scripts\activate.bat
```

or run the environment's executables directly without activating it:

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m streamlit run app_v6_2.py
```

### Ubuntu

```bash
sudo apt update
sudo apt install -y python3 python3-venv python3-pip
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
streamlit run app_v6_2.py
```

After launch, Streamlit normally displays a local address such as **http://localhost:8501**. Open it in your browser. Stop the server with `Ctrl+C`.

## Quick Start

1. **Upload EMG recordings** (`.csv` or `.mat`) and set the correct sampling frequency.
2. **Configure preprocessing** in the sidebar, then inspect the processed signals in **Signal Preprocessing**.
3. **Select a factor-analysis method** and examine latent components, loadings, and reconstructed signals in **Factor Analysis**.
4. Optionally use **Build Multi-view Dataset** and **Factor-analysis Reconstruction Comparison** in the same tab.
5. **Extract and select features** in **Feature Engineering**.
6. **Train and evaluate a classifier** in **Classification Stage**, using matching class labels.
7. Optionally run **Feature-engineering Method × Classifier Benchmark** to compare methods under K-fold or LOSO evaluation.
8. **Export CSV results** from the relevant sections or the **Export** tab.

## Input Data Format

### EMG recordings

CSV files should normally have **rows = time samples** and **columns = EMG channels**:

```csv
EMG_1,EMG_2,EMG_3
0.013,-0.005,0.009
0.021,-0.011,0.014
-0.003,0.008,0.012
```

MATLAB `.mat` files should contain a numeric one- or two-dimensional signal array. The app selects a numeric candidate and may transpose arrays based on their shape; **verify the resulting channel orientation**.

### Classification features and labels

For external classification, provide a feature matrix (`.csv` or `.mat`) and a label vector (`.csv` or `.mat`) with **exactly one label per feature row** and at least two classes.

For LOSO experiments, prepare recordings with correct subject identifiers. When the application infers subject or activity metadata from filenames, review the inferred values before benchmarking.

## Application Layout

| Tab | Main purpose |
| --- | --- |
| **Signal Preprocessing** | Inspect raw/processed EMG and channel quality |
| **Factor Analysis** | Latent-factor methods, reconstruction, synthetic demos, multi-view dataset and reconstruction benchmark |
| **Feature Engineering** | Extract, visualize, select, and export features |
| **Classification Stage** | Train/test classifiers, evaluate metrics, and run feature-engineering × classifier benchmark |
| **Export** | Download available factor and feature results |

## User Manual

A detailed LaTeX manual is available as `EMG_User_Manual_TeXstudio.tex` (if included in your repository). It covers Windows/Ubuntu installation, method descriptions, user-interface controls, experimental workflows, and troubleshooting.

To compile it in **TeXstudio on Windows**, select **Options → Configure TeXstudio → Build → Default Compiler → PdfLaTeX**, then press **F5**. You can also run:

```bash
pdflatex EMG_User_Manual_TeXstudio.tex
pdflatex EMG_User_Manual_TeXstudio.tex
```

## Troubleshooting

| Issue | Suggested action |
| --- | --- |
| `streamlit` not recognized | Activate `.venv` or use `python -m streamlit run app_v6_2.py` |
| `ModuleNotFoundError` | Run `python -m pip install -r requirements.txt` in the active environment |
| `No module named 'PyEMD'` | Run `python -m pip install EMD-signal` |
| Wavelet import error | Run `python -m pip install PyWavelets` |
| Filter cutoff error | Verify the sampling rate and ensure cutoffs are below Nyquist (`fs / 2`) |
| Feature/label length mismatch | Ensure there is exactly one label per feature row |
| LOSO cannot run | Check that the multi-view dataset contains at least two distinct subjects |
| PowerShell execution-policy error | Use `activate.bat` in Command Prompt or call `.venv` executables directly |

## Reproducibility and Research Notes

- Record preprocessing choices, sampling frequency, feature definitions, classifier settings, and library versions for each experiment.
- Prefer **LOSO** when measuring generalization to unseen participants. Random K-fold splits can produce optimistic results when overlapping windows or samples from the same trial appear in both training and testing.
- Inspect automatically identified artifacts and ICA components before removal: true EMG activity may also contain abrupt transients or high-kurtosis components.
- Reconstruction error and classification performance are different objectives; evaluate both when comparing factor-analysis approaches.
- This repository is intended for **research and education**, not as a validated medical diagnostic tool.

## Project Structure

A suggested GitHub repository layout is:

```text
EMG-Processing-and-Factor-Analysis/
├── app_v6_2.py
├── requirements.txt
├── README.md
├── EMG_User_Manual_TeXstudio.tex   # optional
└── .gitignore
```

Consider adding `.venv/`, `__pycache__/`, `.streamlit/secrets.toml`, large raw datasets, and generated outputs to `.gitignore`.


## Acknowledgments

This work was supported under the project I-Démo Régionalisé, Contract No. DOS0243034/00.
