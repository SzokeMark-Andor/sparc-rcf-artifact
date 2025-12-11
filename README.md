# SPARC RAR + NMF Robustness Artifact

This repository hosts a frozen, reproducible artifact for a SPARC–based analysis of the **radial acceleration relation (RAR)** using **non-negative matrix factorization (NMF)** of rotation-curve shapes. The archive contains Python code, selected SPARC inputs, and derived tables and figures used in the RAR + NMF robustness scan and sub-population study.

The aim is to provide a self-contained snapshot of the data products, configuration, and logs so that the experiment can be inspected, reproduced, or extended.

---

## Repository layout

At the top level, this repository contains:

- `artifact_sparc_rar_nmf_dmchemistry_20251119_102307.zip`  
  ZIP archive with the full artifact (code, data, results, internal README).
- `LICENSE`  
  Apache License 2.0 (applies to the code and scripts in this repository).
- `README.md`  
  This file.

All analysis inputs and outputs are stored inside the ZIP archive.

---

## Archive contents (inside the ZIP)

After unpacking `artifact_sparc_rar_nmf_dmchemistry_20251119_102307.zip`, the following main directories are present:

### `code/`

Python scripts that construct the RAR inputs from SPARC, run NMF, and collect group-level statistics:

- `sparc_rar_nmf_robust.py`  
  End-to-end robustness scan over multiple NMF configurations (number of components, radial bin counts, random seeds, dominance thresholds).  
  - Builds the normalized \(v_{\rm obs}^2(r/R_{\max})\) matrix.  
  - Runs NMF for each configuration.  
  - Fits an empirical McGaugh-style RAR  
    \[
      g_{\rm obs} = \frac{g_{\rm bar}}{1 - \exp(-\sqrt{g_{\rm bar}/g^\dagger})}
    \]  
    globally and per NMF-defined group.  
  - Writes group-level RAR parameters (\(g^\dagger\), intrinsic scatter, sample sizes, residual statistics) to CSV files.

- `sparc_rar_nmf_repro.py`  
  Compact script focusing on a single “baseline” configuration (3 components, 15 bins, fixed seed and dominance threshold).  
  Intended for quick reproduction of the core results using the SPARC inputs in `data/`.

- `sparc_rar_nmf_console.py`  
  Console helper for reading individual SPARC rotation-curve files and constructing basic diagnostics.

- `sparc_rar_nmf_comp3_meta_plots.py`  
  Utility for building per-galaxy diagnostics and plots for the low-acceleration subgroup in the baseline configuration (`comp3_dom`), including RAR fits and metadata overlays.

- `sparc_rar_nmf_inspect_comp3.py`  
  Inspection tool that reads SPARC rotation curves for the `comp3_dom` galaxies and computes quantities such as \(R_{\max}\), \(V_{\max}\), and approximate gas fractions, writing the results to CSV files.

Additional helper scripts may be present for plotting or convenience; their roles are documented via comments in each file.

---

### `data/`

SPARC inputs used by the analysis:

- `SPARC_Table1_full.csv`  
  Galaxy-level SPARC metadata (morphological type, distance, inclination, 3.6µm luminosity, HI mass, \(V_{\rm flat}\), quality flag, and references) in CSV form, used for joins with the NMF/RAR outputs.

- `SPARC_Rotmod/`  
  Subdirectory containing SPARC **Rotmod_LTG** rotation-curve files (`*_rotmod.dat`).  
  Each file provides radius, observed circular velocity, errors, and baryonic components (gas, disk, bulge) used to build the normalized \(v_{\rm obs}^2(r/R_{\max})\) profiles.

- `SPARC_Lelli2016c_from_web.txt`  
  ASCII copy of the original SPARC Table 1 as obtained from the public source, kept as a reference for the CSV conversion.

The analysis uses these inputs to derive \(g_{\rm bar}\) and \(g_{\rm obs}\) for each galaxy and radial point.

---

### `results/sparc_rar_nmf_robust/`

Derived tables, summaries, and figures from the robustness experiment:

- **Group-level RAR fits and comparisons**
  - `sparc_rar_nmf_robust_groups.csv`  
    For each NMF configuration (number of components, bins, random seed, dominance threshold) and for each group (`comp1_dom`, `comp2_dom`, `comp3_dom`, `mixed`, `all`), this file stores:  
    - number of galaxies and points,  
    - best-fitting \(g^\dagger\) and its 1σ interval,  
    - intrinsic scatter in log10 space,  
    - residual summary statistics.

  - `sparc_rar_nmf_robust_pairs.csv`  
    For each configuration, records whether the 1σ confidence intervals in \(g^\dagger\) are non-overlapping between pairs of groups. This is used to flag configurations where a group prefers a statistically distinct acceleration scale.

- **Baseline configuration outputs (k = 3 components)**  
  These files correspond to a baseline NMF setup (3 components, fixed binning and dominance threshold) and are intended for direct use in a manuscript:

  - `sparc_rar_nmf_baseline_mixtures.csv`  
    Per-galaxy NMF mixture fractions (`f1`, `f2`, `f3`) and the dominant component label.

  - `sparc_rar_nmf_baseline_all_with_table1.csv`  
    Per-galaxy NMF group assignments merged with SPARC Table 1 metadata.

  - `sparc_rar_nmf_baseline_groups_paper_ready.csv`  
    Compact table of group-level RAR parameters and mean SPARC metadata, prepared for inclusion in a paper.

  - `sparc_rar_nmf_baseline_group_metadata_summary.csv`  
    Summary of mean distance, inclination, luminosity, HI mass, and \(V_{\rm flat}\) for each group.

  - `sparc_rar_nmf_baseline_comp3_galaxies.txt`  
    List of galaxies assigned to the low-acceleration subgroup (`comp3_dom`) in the baseline configuration.

  - `sparc_rar_nmf_baseline_comp3_galaxies_diagnostics.csv`  
    Basic structural diagnostics for the `comp3_dom` galaxies (e.g. \(R_{\max}\), \(V_{\max}\), gas-fraction proxy, NMF mixture fractions).

  - `sparc_rar_nmf_baseline_comp3_galaxies_full_diagnostics.csv` and  
    `sparc_rar_nmf_baseline_comp3_with_table1.csv`  
    Extended diagnostics combining RAR parameters and SPARC metadata for the `comp3_dom` galaxies.

- **Configuration-level robustness and LaTeX tables**

  - `sparc_rar_nmf_comp3_config_summary.csv`  
    Per-configuration summary of how many galaxies fall into `comp3_dom`, their group-level \(g^\dagger\) range, and non-overlap flags vs other groups.

  - `sparc_rar_nmf_baseline_groups_table.tex`  
    LaTeX table summarizing RAR parameters and mean metadata for each group in the baseline configuration.

  - `sparc_rar_nmf_comp3_config_table.tex`  
    LaTeX table summarizing the robustness of the `comp3_dom` subgroup across the scanned NMF configurations.

- **Text summaries**

  - `sparc_rar_nmf_robust_summary.txt`  
    Narrative overview of the dataset, RAR model, NMF setup, and global results, including approximate values for the global RAR and group-level acceleration scales.

  - `sparc_rar_nmf_comp3_nonoverlap_summary.txt`  
    Short text summary reporting how often the low-acceleration subgroup (`comp3_dom`) has non-overlapping 1σ intervals in \(g^\dagger\) compared to other groups across the scanned configurations.

- **Figures**

  - `sparc_rar_nmf_robust_rar_baseline.png`  
    Baseline RAR plot for the full sample and groups.

  - `sparc_rar_nmf_baseline_residual_box.png`, `sparc_rar_nmf_baseline_residual_hist.png`  
    Residual distributions (boxplot and histogram) for the baseline model.

  - `comp3_inspect_plots/*.png`  
    Per-galaxy plots for the `comp3_dom` subgroup, showing rotation curves and RAR fits.

---

### `README/`

- `README_sparc_rar_nmf.txt`  
  Internal documentation for the artifact.  
  This file explains in more detail:
  - how the SPARC data were used and filtered,  
  - the exact NMF and RAR fitting settings,  
  - how each code and results file fits into the overall pipeline.

This internal README should be treated as the primary reference for running the scripts and interpreting the outputs.

---

## Reproducibility notes

- The original analysis was run on a standard Python scientific stack (NumPy, pandas, scikit-learn, matplotlib, astropy) together with PowerShell-based orchestration on Windows.  
- The Python code is platform-independent and should run on any modern Linux, macOS, or Windows environment once dependencies are installed.  
- The CSV, TXT, and PNG files in `results/` are pre-generated so that key values and plots can be inspected without re-running the pipeline.

For detailed execution instructions and recommended workflows, see `README/README_sparc_rar_nmf.txt` inside the archive.

---

## Scientific scope (brief)

The artifact is focused on:

- deriving \(g_{\rm bar}\) and \(g_{\rm obs}\) from SPARC rotation curves,  
- fitting an empirical RAR model globally and within NMF-defined sub-populations of galaxies,
- scanning a grid of NMF configurations and group-definition thresholds,
- quantifying how often a small subgroup with a distinct RAR acceleration scale appears and how robust this subgroup is across configurations.

The artifact is intended for transparency and reproducibility of this specific experiment; it is not a general-purpose SPARC analysis library.

---

## License

Code and scripts in this repository are released under the **Apache License 2.0** (see `LICENSE`).

Data files derived from SPARC or other external surveys retain the original copyrights
and usage conditions specified by their authors. Any reuse of those data should follow
the upstream terms.
