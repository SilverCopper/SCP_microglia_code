# SCP Microglia analysis notebooks

This directory contains the analysis notebooks for the SCP Microglia project.
The notebooks are organized as a staged workflow that starts from DIA report
processing and ends with downstream biological analyses.

## Recommended order

Run the notebooks in this order when reproducing the full workflow:

1. `1_1_io.ipynb` - read Spectronaut/DIA reports, build gene-level matrices,
   compare detected genes, and prepare initial AnnData objects.
2. `1_2_qc.ipynb` - calculate cell and feature QC metrics, inspect missing
   values, and generate QC plots.
3. `1_3_preprocessing.ipynb` - normalize protein matrices, prepare log2
   normalized layers, select highly variable genes, and run baseline embedding.
4. `1_4_batch_correction.ipynb` - run manual batch-correction workflows and
   benchmarking. This notebook intentionally keeps the original explicit
   Scanpy/scVI/R workflow and does not call `sd.pp.integrate()`.
5. `1_4_scPROTEIN_part.ipynb` - run the scPROTEIN-specific analysis segment.
6. `1_5_clustering.ipynb` - cluster the batch-corrected data and summarize
   cluster-level patterns.
7. `2_1_de_analysis.ipynb` - run differential testing, enrichment, volcano
   plots, marker summaries, and related downstream analyses.
8. `2_2_pseudotime_analysis.ipynb` - run trajectory and pseudotime analyses.
9. `2_3_glue.ipynb` - integrate protein and RNA data with GLUE.

## Main dependencies

- `scdia` is used by the IO, QC, preprocessing, clustering, and differential
  analysis notebooks.
- Scanpy and AnnData are used throughout the workflow.
- The batch-correction notebook also uses optional tools such as Harmony,
  BBKNN, Scanorama, scVI, R-backed integration methods, and scIB metrics.
- `1_4_scPROTEIN_part.ipynb` depends on scPROTEIN.
- `2_2_pseudotime_analysis.ipynb` depends on CellRank and scVelo.
- `2_3_glue.ipynb` depends on scGLUE and its graph-learning dependencies.

Install `scdia` before running notebooks that import it:

```bash
git clone https://github.com/SilverCopper/scDIA.git
cd scDIA
pip install -e .
```

Install optional extras as needed for enrichment, imputation, batch correction,
or scVI workflows:

```bash
pip install -e ".[enrichment]"
pip install -e ".[impute]"
pip install -e ".[python-integration]"
pip install -e ".[scvi]"
pip install -e ".[all]"
```

## Inputs and outputs

The notebooks expect project input data under the project data directories and
external raw-data locations configured near the top of each notebook. Generated
figures are written under `SCP_Microglia/figures/`, and intermediate result
tables or AnnData outputs are written under `SCP_Microglia/results/`.

Large raw data files, trained models, and generated analysis outputs are not
stored in this notebook directory. Review and update the path variables in the
first code cell of each notebook before rerunning the workflow on a new system.

## Compatibility notes

The notebooks use the current `scdia` public module layout:

```python
import scdia as sd

sd.io  # report readers and matrix builders
sd.qc  # QC metric calculations
sd.pp  # preprocessing helpers
sd.tl  # differential testing and enrichment
sd.pl  # plotting
```

Plotting helpers live in `sd.pl`, while QC calculations remain in `sd.qc`.
Differential testing results are stored in `adata.uns[key_added]` and consumed
by enrichment and volcano plotting through the matching `de_key`.
