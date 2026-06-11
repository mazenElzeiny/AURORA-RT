# Cherenkov 3-Band TOPAS Model (Step 2)

This README describes the validated model in Cherenkov_3band_wavelength_debug.txt.


## Most Important: Model Dimensions

- World physical size: 2.0 m x 2.0 m x 2.0 m
  - Defined by half-lengths HLX=HLY=HLZ=1.0 m
- Water phantom physical size: 30 cm x 30 cm x 30 cm
  - Defined by half-lengths HLX=HLY=HLZ=15.0 cm
- Scoring grid dimensions (for all fluence scorers): 30 x 30 x 60 voxels
- Voxel size: 1.0 cm x 1.0 cm x 0.5 cm
- Total scored voxels per file: 54,000

## Current Run Configuration

- Source particle: gamma
- Beam energy: 6.0 MeV
- Histories per run: 1,000,000
- Threads: 1
- Random seed: 12345

## Optical Bands Scored

- Fluence_OpticalAll.csv: opticalphoton fluence (no KE filter)
- Fluence_630nm.csv: 615-645 nm (1.9223 to 2.0160 eV)
- Fluence_700nm.csv: 685-715 nm (1.7340 to 1.8100 eV)
- Fluence_850nm.csv: 835-865 nm (1.4334 to 1.4849 eV)

## Run Command (PowerShell)

wsl -e bash -lc "export TOPAS_G4_DATA_DIR=/home/mazen/topas/G4Data; cd /mnt/c/Users/HP/Desktop/Projects/MedicalPhysc\ Comp; /home/mazen/topas/bin/topas Cherenkov_3band_wavelength_debug.txt"

## Expected Output Files

- Fluence_OpticalAll.csv
- Fluence_630nm.csv
- Fluence_700nm.csv
- Fluence_850nm.csv

## Recent updates (May 2026)

- Added explicit 3-band fluence scoring outputs (630/700/850 nm) written as CSV by the model when run.
- A temporary unfiltered optical map `Fluence_OpticalAll.csv` is included for debugging and verification.
- A resampled dose volume was generated from the high-resolution `DoseRef.npy` and saved as:
  - `Dose_resampled_30x30x60.npy`
  - `Dose_generated_from_resampled.csv` (format: `x, y, z, Dose`)

## Git / Sharing suggested workflow

To publish these updates to your GitHub repository, run (PowerShell):

```powershell
git add Cherenkov_3band_wavelength_debug.txt \
    README_Cherenkov_3band_wavelength_debug.md \
    Dose_resampled_30x30x60.npy \
    Dose_generated_from_resampled.csv
git commit -m "Add 3-band fluence scoring, resampled dose artifacts, and README updates"
git push origin your-branch-name
```

Create a Pull Request describing:
- What changed: added 3-band fluence scorers and resampled dose files
- Why: validated gamma-source model produces non-zero fluence across bands; resampled dose provided for cross-checks and downstream analysis
- How to validate: run the model (see Run Command), check the `Fluence_*.csv` files exist and inspect `Dose_generated_from_resampled.csv` for the 30x30x60 grid

If you want me to create the commit and open a PR, I can prepare the git commands here; I won't run them without your confirmation (credentials/push rights required).

