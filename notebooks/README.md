# Notebooks

Introductory notebooks for the downscaling hackathon. Work through them in order.

| # | Notebook | What you learn | Needs |
|---|----------|----------------|-------|
| 1 | [`01_intro_ml_and_data.ipynb`](01_intro_ml_and_data.ipynb) | ML vocabulary (features/targets, train/val/test, loss, **encoder / latent space / decoder**, autoencoder), a tour of the WeatherGenerator architecture, and hands-on exploration of the ERA5 data with the **anemoi** library (open a dataset, inspect metadata, plot fields on the O96 reduced-Gaussian grid, normalize with dataset statistics). | minimal |
| 2 | [`02_downscaling_baseline_bilinear.ipynb`](02_downscaling_baseline_bilinear.ipynb) | What downscaling is, how to load the **N320 (~0.25°)** target grid, and a **bilinear interpolation baseline** from the O96 grid to the N320 points. Includes a self-supervised *coarsen-then-recover* evaluation so you can put an RMSE number on the baseline. | minimal |

A later notebook (3) will drive the WeatherGenerator model for the
actual **learned** downscaling. That one needs the full WeatherGenerator install and is
**not** covered by the minimal setup below.

## Setup

Notebooks 1 and 2 only need a lightweight scientific-Python stack — no PyTorch, no
WeatherGenerator. **A ready-to-use environment and Jupyter kernel are already installed on
Levante at `/work/bk1444/hackathon_2026/challenge_3/`.**

On JupyterHub, register the shared kernel once for your user (this writes a per-user
kernelspec that points at the shared environment), then pick it in the notebook:

```bash
/work/bk1444/hackathon_2026/challenge_3/env/bin/python -m ipykernel install --user \
  --name hackathon-downscaling --display-name "Python (hackathon-downscaling)"
```

Then open a notebook and pick **Python (hackathon-downscaling)** (top-right *Select Kernel*).
In VS Code you can instead select the interpreter
`/work/bk1444/hackathon_2026/challenge_3/env/bin/python` directly.

Prefer your own environment? Build it from [`requirements.txt`](../requirements.txt) (or the
provided `pyproject.toml`) — see the [repository README](../README.md), which also has data
paths and the full challenge description.
