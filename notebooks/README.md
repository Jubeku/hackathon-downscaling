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
WeatherGenerator. From the repository root:

```bash
cd ..                                # hackathon-downscaling/
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name hackathon-downscaling
```

Then open a notebook and pick the **hackathon-downscaling** kernel (top-right
*Select Kernel*).

See the [repository README](../README.md) for data paths and the full challenge
description.
