# Notebooks

Introductory notebooks for the downscaling hackathon. Work through them in order.

| # | Notebook | What you learn | Needs |
|---|----------|----------------|-------|
| 1 | [`01_intro_ml_and_data.ipynb`](01_intro_ml_and_data.ipynb) | ML vocabulary (features/targets, train/val/test, loss, **encoder / latent space / decoder**, autoencoder), a tour of the WeatherGenerator architecture, and hands-on exploration of the ERA5 data with the **anemoi** library (open a dataset, inspect metadata, plot fields on the O96 reduced-Gaussian grid, normalize with dataset statistics). | minimal |
| 2 | [`02_downscaling_baseline_bilinear.ipynb`](02_downscaling_baseline_bilinear.ipynb) | What downscaling is, how to load the **N320 (~0.25°)** target grid, and a **bilinear interpolation baseline** from the O96 grid to the N320 points. Includes a self-supervised *coarsen-then-recover* evaluation so you can put an RMSE number on the baseline. | minimal |

A later notebook (3) will drive the WeatherGenerator model for the actual **learned**
downscaling. It needs `torch` + WeatherGenerator, which the shared environment below already
provides — so the same one-time setup covers all three notebooks.

## Setup

**One environment for everything.** A single shared WeatherGenerator environment and the
`hackathon-downscaling` Jupyter kernel are already installed on Levante and cover all the
notebooks *and* the later learned downscaling / training. Notebooks 1 and 2 only exercise a
lightweight subset (`anemoi`, `scipy`, `xarray`, `matplotlib`, `cartopy`); notebook 3 also
uses `torch` + WeatherGenerator, which the same environment already provides.

The easiest way to get set up is the workshop helper, which registers the kernel and creates
your working/scratch/data links in one go:

```bash
module load clint workshops
workshops run expect_hackathon setup c3
```

Then open a notebook and pick **Python (hackathon-downscaling)** (top-right *Select Kernel*).

Not using the helper? Register the shared kernel once for your user (writes a per-user
kernelspec pointing at the shared env — it does not copy or rebuild it):

```bash
/work/bk1444/hackathon_2026/challenge_3/WeatherGenerator/.venv/bin/python -m ipykernel install \
  --user --name hackathon-downscaling --display-name "Python (hackathon-downscaling)"
```

In VS Code you can instead select the interpreter
`/work/bk1444/hackathon_2026/challenge_3/WeatherGenerator/.venv/bin/python` directly.

Prefer your own environment? See the [repository README](../README.md), which also has data
paths and the full challenge description.
