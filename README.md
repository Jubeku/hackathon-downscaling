# Hackathon: Downscaling atmospheric states with the WeatherGenerator

**Challenge.** Use the WeatherGenerator (WG) model to *downscale* ERA5 atmospheric
states from **1°** (O96 reduced Gaussian, ~40k grid points) to **0.25°**
(**N320** reduced Gaussian, ~542k grid points). We repurpose the WG model — which we
normally use for *time advancement* (predict the next 6-hourly state conditioned on the
previous one) — to instead produce high-resolution detail conditioned on the
low-resolution state.

Before touching the model, participants first get comfortable with the **data** and the
**ML vocabulary**. These notebooks build that foundation and establish a simple
**bilinear-interpolation baseline** that the learned WG downscaling will later be
compared against.

## Notebooks

| # | Notebook | What you learn |
|---|----------|----------------|
| 1 | [`notebooks/01_intro_ml_and_data.ipynb`](notebooks/01_intro_ml_and_data.ipynb) | ML vocabulary (features/targets, train/val/test, loss, **encoder / latent space / decoder**, autoencoder), a tour of the WeatherGenerator, and hands-on exploration of the ERA5 data with the **anemoi** library. |
| 2 | [`notebooks/02_downscaling_baseline_bilinear.ipynb`](notebooks/02_downscaling_baseline_bilinear.ipynb) | What downscaling is, how to load the **N320 (~0.25°)** target grid, and a **bilinear interpolation baseline** from the O96 grid to the N320 points, **scored (RMSE) against a real N320 target**. Data paths are set in one configurable cell so the whole notebook can be repointed at other data with a one-line change. |

A follow-up notebook (later) will show how to drive the WeatherGenerator model for the
actual learned downscaling.

## Setup

Notebooks 1 and 2 only need a lightweight scientific-Python stack (no PyTorch, no
WeatherGenerator). **A ready-to-use environment and Jupyter kernel are already installed on
Levante, so you normally do not need to build anything.** (Installing the full
WeatherGenerator — required for the later learned downscaling notebook — comes later.)

### Use the pre-built kernel (recommended)

A shared environment and kernel spec live under `/work/bk1444/hackathon_2026/challenge_3/`:

- environment: `/work/bk1444/hackathon_2026/challenge_3/env`
- kernel spec: `/work/bk1444/hackathon_2026/challenge_3/kernel/share/jupyter/kernels/hackathon-downscaling`

**On JupyterHub**, register the shared kernel once for your user so it appears in the
launcher / *Select Kernel* menu. This writes a per-user kernelspec that points at the shared
environment — it does **not** copy or rebuild it:

```bash
/work/bk1444/hackathon_2026/challenge_3/env/bin/python -m ipykernel install --user \
  --name hackathon-downscaling --display-name "Python (hackathon-downscaling)"
```

Then reload the page and pick **Python (hackathon-downscaling)**.

**In VS Code**, you can instead select the interpreter directly (top-right *Select Kernel* →
*Select Another Kernel* → *Python Environments*):

```
/work/bk1444/hackathon_2026/challenge_3/env/bin/python
```

### Build your own environment (optional)

On another machine, or if you prefer your own copy, install the dependencies from
[`requirements.txt`](requirements.txt) (or the provided `pyproject.toml` / `uv.lock`):

```bash
cd hackathon-downscaling
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name hackathon-downscaling
```

Then select the **hackathon-downscaling** kernel in the notebook (top-right
*Select Kernel* button).

### Alternative: reuse the WeatherGenerator environment

If you already have the WeatherGenerator virtual environment, it also has everything the
first two notebooks need (`anemoi`, `scipy`, `cartopy`, `xarray`, `matplotlib`, `torch`):

```bash
cd ../WeatherGenerator
source .venv/bin/activate            # kernel name: "weathergen"
```

In VS Code / Jupyter, select the matching interpreter as the notebook kernel (top-right
*Select Kernel* button).

## Data

All paths are set in one config cell (`DATA_ROOT` + dataset names), so switching later is a one-line change.

- **Coarse source (O96, ~1°):**
  `${DATA_ROOT}/aifs-ea-an-oper-0001-mars-o96-1979-2023-6h-v8.zarr`
  — 101 variables, 6-hourly, 1979–2023, O96 octahedral reduced Gaussian grid (40 320 points).

- **Fine target (N320, ~0.25°):**
  `${DATA_ROOT}/aifs-ea-an-oper-0001-mars-n320-1979-2023-6h-v8.zarr`
  — same variables/dates, reduced Gaussian grid with 640 latitude rings and 542 080 points.

`DATA_ROOT` defaults to `/work/bk1444/hackathon_2026/challenge_3/` and can be overridden via the
`DATA_ROOT` environment variable (path may change completely on a different HPC).

> Notebook 1 opens the 1° (O96) ERA5 dataset for the data tour; the downscaling
> baseline (notebook 2) uses the ERA5 O96/N320 pair above.
