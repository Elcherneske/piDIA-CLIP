# πDIA-CLIP: Efficient Identification of Highly Heterogeneous Proteomics Data via a Generalized Zero-Shot Framework

[![bioRxiv](https://img.shields.io/badge/bioRxiv-2026.02.09.704949-brightgreen)](https://doi.org/10.64898/2026.02.09.704949)
[![Zenodo](https://img.shields.io/badge/Zenodo-10.5281%2Fzenodo.18863866-blue)](https://doi.org/10.5281/zenodo.19059840)

## Description

**πDIA-CLIP** (Data-Independent Acquisition with Contrastive Learning Integrated Proteomics) is a generalized framework for zero-shot DIA-MS analysis. The prefix **π** denotes the [π-HuB project](https://doi.org/10.1038/s41586-024-08249-7), a global initiative for proteomics research.

If you use πDIA-CLIP in your work, please cite:

> Liao Y, Li Y, Xiao Z, Miao C, Zhao X, Zhang Y, Wen H, E W, Chang C, Zhang W. πDIA-CLIP: efficient identification of highly heterogeneous proteomics data via a generalized zero-shot framework. *bioRxiv* (2026). https://doi.org/10.64898/2026.02.09.704949

## Key Features

- **Zero-shot inference-only architecture** — no run-specific semi-supervised re-training; plug-and-play PSM re-scoring after standard RT calibration and XIC extraction
- **Cross-modal contrastive learning** — aligns peptide sequences and multi-dimensional XIC signals in a shared latent space via a transformer-based sequence encoder and a specialized spectral encoder
- **Hybrid encoder–decoder scoring** — combines aligned latent features with co-elution statistics for calibrated PSM scores and fragment-based quantification
- **Broad applicability** — validated on bulk DIA, multi-species mixtures, metaproteomics, spatial proteomics, and single-cell proteomics
- **Hardware-agnostic efficiency** — CPU and GPU inference; substantially faster than existing tools

## Workflow

πDIA-CLIP integrates into the standard peptide-centric DIA-MS workflow at the **PSM re-scoring** stage. Retention-time calibration and peak-group generation can be performed by mainstream tools (e.g., DIA-NN, MaxDIA, Spectronaut). πDIA-CLIP then extracts precursor and fragment XICs and performs zero-shot re-scoring, FDR estimation, and quantification.

```
DIA-MS raw data → RT calibration / XIC extraction (DIA-NN or compatible tools)
                → πDIA-CLIP zero-shot PSM re-scoring & quantification
                → Target-decoy FDR filtering → Identification & quantification tables
```

## Software

| Component | Description |
|-----------|-------------|
| **Command-line inference** | This repository (`scripts/infer_script.py`) |
| **GUI application** | Pre-built executables with graphical interface — [Releases](https://github.com/Elcherneske/DIA-CLIP/releases/) |
| **Agent system** | Model-based agent for interactive analysis — [Web access](http://otfo1466981.bohrium.tech:50002/) |

Training data, benchmark results, and example files (mzML, spectral library, model weights) are available on [Zenodo](https://doi.org/10.5281/zenodo.18863866).

---

## Requirements

- Python 3.x (python 3.12 is recommended)
- CUDA is recommended for faster inference (optional; CPU is supported)
- `make` utility is required (for executing make commands; install via your package manager if not already present)

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/Elcherneske/DIA-CLIP.git
cd DIA-CLIP
```

### 2. Create a virtual environment (conda is recommended)

```bash
conda create -n dia-clip python=3.12
conda activate dia-clip
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

`requirements.txt` includes PyTorch, transformers, pandas, scipy, and OpenMSUtils (installed from GitHub).

---

## Configuration

Inference is driven by a **config file**. Use `configs/infer.config` as a reference.

### Example config

Create or edit a `.config` file in the project root or under `configs/`:

```ini
[General]
device = cpu          # Compute device: cpu or cuda
dtype = float         # Model precision: float is supported
threads = 8           # Number of threads
out_dir = xxx         # Output directory (set to your path)

[Preprocess]
diann_dir = ./diann/      # DIA-NN directory; usually leave as is
library_path = xxx.tsv    # Path to spectral library (.tsv)
mzml_path = 1.mzML;2.mzML # mzML file(s) to analyze; use ; to separate multiple files

[Database]
batch_size = 1024     # Inference batch size

[Infer]
checkpoint_path = xxx.pt  # Path to model weights (.pt)
FDR = 0.01            # FDR threshold (e.g. 0.01 for 1%)
```

### Required options

| Option | Description |
|--------|-------------|
| `out_dir` | Output directory; must exist or be creatable |
| `library_path` | Full path to the spectral library (.tsv) |
| `mzml_path` | One or more mzML file paths, separated by `;` |
| `checkpoint_path` | Path to the pretrained model checkpoint (.pt) |

---

## Usage

### Example Data

Example data, including mzML files, a DIA-NN generated spectral library (.tsv), and model weights (.pt), is available from [Zenodo (10.5281/zenodo.18863866)].

### 1. Prepare data

- **mzML files**: DIA mass spectrometry raw data (.mzML is supported).
- **Spectral library**: A peptide spectral library (.tsv) generated by DIA-NN.
- **Model weights**: A `.pt` checkpoint.

### 2. Edit the config

Copy and modify `configs/infer.config` (or your own config), and set the paths, for example:

```ini
[General]
out_dir = ./results

[Preprocess]
library_path = /path/to/your/library.tsv
mzml_path = /path/to/sample1.mzML;/path/to/sample2.mzML

[Infer]
checkpoint_path = /path/to/model.pt
```

### 3. Run inference

From the project **root directory**:

```bash
cd DIA-CLIP
sh scripts/build.sh
python scripts/infer_script.py --config configs/infer.config
```

### 4. Output

For each mzML file, two files are written under `out_dir`:

- **`.all.tsv`**: All peptide inference and quantification results.
- **`.fdr.tsv`**: Peptides passing the configured FDR threshold.

Example: with `mzml_path = sample1.mzML` and `out_dir = ./results` you get:

- `results/sample1.all.tsv`
- `results/sample1.fdr.tsv`

---

## Citation

```bibtex
@article{Liao2026piDIACLIP,
  title   = {πDIA-CLIP: efficient identification of highly heterogeneous proteomics data via a generalized zero-shot framework},
  author  = {Liao, Yucheng and Li, Yongge and Xiao, ZeXu and Miao, ChenChen and Zhao, Xingpu and Zhang, Yuanyuan and Wen, Han and E, Weinan and Chang, Cheng and Zhang, Weijie},
  journal = {bioRxiv},
  year    = {2026},
  doi     = {10.1101/2026.02.09.704949}
}
```

## Contact

For questions or collaboration inquiries, please contact the corresponding authors:

- Cheng Chang — changcheng@ncpsb.org.cn
- Weijie Zhang — zhangwj@aisi.ac.cn
