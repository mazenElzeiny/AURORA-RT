<div align="center">

# 🩺 Real-Time Oxygen-Guided Adaptive Radiation Therapy

**A Digital Twin simulation framework for Cherenkov-guided, hypoxia-mediated adaptive radiotherapy**

Integrating TOPAS Monte Carlo · NIRFAST optical diffusion · Virtual ICCD sensing · Deliverability-constrained dose optimization

![Python](https://img.shields.io/badge/Python-3.9+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![TOPAS](https://img.shields.io/badge/TOPAS-v3.7-00897B?style=for-the-badge)
![Geant4](https://img.shields.io/badge/Geant4-10.7-1565C0?style=for-the-badge)
![NIRFAST](https://img.shields.io/badge/NIRFAST-optical--diffusion-6A1B9A?style=for-the-badge)
![License](https://img.shields.io/badge/License-Academic-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Proof--of--Concept-brightgreen?style=for-the-badge)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Results](#-key-results)
- [TOPAS Model Configuration](#-topas-model-configuration)
- [Setup Instructions](#️-setup-instructions)
  - [1. Clone NIRFAST](#1-clone-nirfast)
  - [2. Place the Demo Notebook](#2-place-the-demo-notebook)
  - [3. Add the Data Files](#3-add-the-data-files)
- [Running the Demo](#-running-the-demo)
- [Project Structure](#-project-structure)
- [Team](#-team)

---

## 🔬 Overview

This project implements a four-phase Digital Twin pipeline for in silico prototyping of biologically adaptive radiotherapy driven by real-time Cherenkov emission:

| Phase | Module | Description |
|-------|--------|-------------|
| ⚛️ **I — Physics** | TOPAS / Geant4 | Monte Carlo radiation transport, secondary electron tracking & Cherenkov photon generation via Frank–Tamm model |
| 🌊 **II — Sensing** | NIRFAST + ICCD model | Optical diffusion transport to tissue surface; virtual intensified CCD camera with shot & read noise |
| 🧮 **III — Recovery** | Red Reference + NNLS | Beer-Lambert melanin correction across 100 Fitzpatrick skin phenotypes; affine calibration for low-dose regimes |
| 🎯 **IV — Adaptation** | Dose optimizer | Hypoxia classification at 70% StO₂ threshold; locked dose-weighted enhancement (α = 0.12) |

---

## 📊 Key Results

Results on the **TOPAS TumorPhantom** (10⁷ particle histories — low-dose stress-test regime):

| Metric | Value |
|--------|-------|
| 🎯 Calibrated StO₂ recovery — MAE | **1.55 pp** |
| 📈 Calibrated StO₂ recovery — R² | **0.964** (N = 1534 voxels) |
| ✅ Hypoxia classification — Precision | **0.993** |
| 🔁 Hypoxia classification — Recall | **0.979** |
| 🏅 Hypoxia classification — F1 | **0.986** |
| 🟩 Confusion matrix | TP=139 · FP=1 · FN=3 · TN=1391 |
| 💉 Hypoxic burden reduction (α=0.12) | **63.38%** (142 → 52 voxels) |

> Computational benchmark under idealized conditions (comparative reference): R²=0.97, MAE=0.80 pp, hypoxia reduction 86.7% over 10 fractions.

---

## ⚙️ TOPAS Model Configuration

The Cherenkov 3-band wavelength model (`Cherenkov_3band_wavelength_debug.txt`) used to generate all input fluence files is configured as follows:

### 🏗️ Geometry

| Parameter | Value |
|-----------|-------|
| World volume | 2.0 m × 2.0 m × 2.0 m (half-lengths: 1.0 m each) |
| Water phantom | 30 cm × 30 cm × 30 cm (half-lengths: 15.0 cm each) |
| Scoring grid | 30 × 30 × 60 voxels |
| Voxel size | 1.0 cm × 1.0 cm × 0.5 cm |
| Total scored voxels (per file) | 54,000 |

### 🔦 Beam & Run Parameters

| Parameter | Value |
|-----------|-------|
| Source particle | gamma |
| Beam energy | 6.0 MeV |
| Histories per run | 1,000,000 |
| Threads | 1 |
| Random seed | 12345 |

### 🌈 Optical Bands Scored

| Output File | Band | Energy Range |
|-------------|------|-------------|
| `Fluence_OpticalAll.csv` | All optical photons (no KE filter) | — |
| `Fluence_630nm.csv` | 615 – 645 nm | 1.9223 – 2.0160 eV |
| `Fluence_700nm.csv` | 685 – 715 nm | 1.7340 – 1.8100 eV |
| `Fluence_850nm.csv` | 835 – 865 nm | 1.4334 – 1.4849 eV |

### 🖥️ Run Command (WSL / PowerShell)

```powershell
wsl -e bash -lc "export TOPAS_G4_DATA_DIR=/home/mazen/topas/G4Data; cd /mnt/c/Users/HP/Desktop/Projects/MedicalPhysc\ Comp; /home/mazen/topas/bin/topas Cherenkov_3band_wavelength_debug.txt"
```

### 📁 Expected Output Files

```
Fluence_OpticalAll.csv
Fluence_630nm.csv
Fluence_700nm.csv
Fluence_850nm.csv
Dose_resampled_30x30x60.npy        ← resampled from DoseRef.npy
Dose_generated_from_resampled.csv  ← format: x, y, z, Dose
```

> 💡 `Fluence_OpticalAll.csv` is included for debugging and cross-verification of band-filtered outputs.
> The resampled dose volume was generated from the high-resolution `DoseRef.npy` onto the 30×30×60 voxel grid to match fluence scorer dimensions.

---

## 🛠️ Setup Instructions

### 1. Clone NIRFAST

Clone the NIRFAST repository from GitHub:

```bash
git clone <NIRFAST_GITHUB_LINK_HERE>
```

> 📌 **Replace `<NIRFAST_GITHUB_LINK_HERE>`** with the actual NIRFAST repository URL before publishing.

After cloning, navigate into `nirfast-uff/demo/` — all following steps apply inside that folder.

---

### 2. Place the Demo Notebook

Replace the existing `demo_start.ipynb` inside the `demo` folder with the updated notebook from this repository:

```
nirfast-uff/
└── demo/
    └── demo_start.ipynb   ← 🔁 replace this with the file from this repo
```

Via terminal:

```bash
cp path/to/this/repo/demo_start.ipynb path/to/nirfast-uff/demo/demo_start.ipynb
```

---

### 3. Add the Data Files

A `data.zip` archive is provided in this repository containing all fluence maps, dose files, and supporting CSVs required by the notebook.

**Steps:**

1. 📥 Download `data.zip`
2. 📂 Extract its contents
3. 📋 Place **all extracted files** directly inside `nirfast-uff/demo/`:

```
nirfast-uff/
└── demo/
    ├── demo_start.ipynb
    ├── Fluence_OpticalAll.csv
    ├── Fluence_630nm.csv
    ├── Fluence_700nm.csv
    ├── Fluence_850nm.csv
    ├── Dose_resampled_30x30x60.npy
    ├── Dose_generated_from_resampled.csv
    └── ... (all other extracted files)
```

> ⚠️ **Important:** Do **not** place files inside a nested subfolder. They must sit at the **same level** as `demo_start.ipynb`.

---

## 🚀 Running the Demo

Once setup is complete:

```bash
# Navigate to the demo folder
cd path/to/nirfast-uff/demo

# Launch Jupyter
jupyter notebook demo_start.ipynb
```

Run all cells **in order** — the notebook is self-contained and will:
- Load fluence and dose maps
- Apply melanin correction across 100 skin phenotypes
- Run the non-circular Beer-Lambert validation
- Produce all result figures and CSV summaries automatically

**🐍 Dependencies:** Python 3.9+, NumPy, SciPy, Pandas, Matplotlib — standard scientific Python packages.

---

## 📁 Project Structure

```
.
├── 📓 demo_start.ipynb                    # Main demo notebook → place in nirfast-uff/demo/
├── 🗜️  data.zip                            # Input data archive → extract into nirfast-uff/demo/
├── 🐍 patch_cell_v2.py                    # Non-circular DPF validation patch cell
├── 📄 Cherenkov_3band_wavelength_debug.txt # TOPAS model definition file
├── 📋 README_Cherenkov_3band_wavelength_debug.md  # TOPAS model documentation
└── 📖 README.md                           # This file
```

---

## 👥 Team

Developed by students of the **Biomedical Engineering Department, Cairo University**
under the supervision of **Dr. Sherif ElGohary**

| 👤 Name | 🔗 LinkedIn | 💻 GitHub |
|---------|------------|----------|
| <!-- Mazen Mohamed --> | [LinkedIn](#) | [GitHub](#) |
| <!-- Aya Sayed --> | [LinkedIn](#) | [GitHub](#) |
| <!-- Maryam Moustafa --> | [LinkedIn](#) | [GitHub](#) |
| <!-- Engy Wael --> | [LinkedIn](#) | [GitHub](#) |
| <!-- Engy Mohamed --> | [LinkedIn](#) | [GitHub](#) |

> 📌 **Fill in names, LinkedIn profile URLs, and GitHub profile URLs above before publishing.**

---

<div align="center">

🏛️ **Cairo University · Faculty of Engineering · Biomedical Engineering Department**

Supervised by Dr. Sherif ElGohary — [Sh.elgohary@eng1.cu.edu.eg](mailto:Sh.elgohary@eng1.cu.edu.eg)

</div>
