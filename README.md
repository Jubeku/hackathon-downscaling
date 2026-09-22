# Hackathon: Downscaling atmospheric states with the WeatherGenerator

**Challenge.** Use the WeatherGenerator (WG) model to *downscale* ERA5 atmospheric
states from **1°** (O96 reduced Gaussian, ~40k grid points) to **0.25°**
(**N320** reduced Gaussian, ~542k grid points). We repurpose the WG model — which allows for multi-dataset inputs and outputs to predict high-resolution states based on low-resolution inputs.

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

**One environment for everything.** A single shared WeatherGenerator environment (and the
`hackathon-downscaling` Jupyter kernel) is already installed on Levante and covers the intro
notebooks *and* the learned downscaling / training — so you normally do not need to build
anything. Notebooks 1 and 2 only exercise a lightweight subset (`anemoi`, `scipy`, `xarray`,
`matplotlib`, `cartopy`); the later notebook additionally uses `torch` + WeatherGenerator,
which the same environment already provides.

### One-command setup on Levante (recommended)

A single shared environment now serves **everything** — the intro notebooks *and*
the WeatherGenerator downscaling/training. Set it up with the workshop helper:

```bash
module load clint workshops
workshops run expect_hackathon setup c3
```

This one command:

- clones this repo to `~/hackathon-downscaling`,
- registers the shared **`hackathon-downscaling`** Jupyter kernel (a WeatherGenerator
  environment with `torch`, `anemoi`, `cartopy`, `scipy`, `xarray`, `matplotlib`, …),
- creates `~/work_c3` → your per-user working dir on `/work/.../challenge_3/$USER`,
- creates `~/scratch_c3` for large/temporary output,
- links `~/data_c3` to the shared ERA5 datasets.

Then open a notebook and pick **Python (hackathon-downscaling)**. The same kernel/env
is used for the learned downscaling notebook and for launching training (see
`/work/bk1444/hackathon_2026/challenge_3/launch/README.md`).

To undo the links (leaves your repo, work and scratch dirs untouched):

```bash
workshops run expect_hackathon clean c3
```

### Manual kernel registration (if not using the workshop helper)

The shared environment and kernel spec live under `/work/bk1444/hackathon_2026/challenge_3/`:

- environment: `/work/bk1444/hackathon_2026/challenge_3/WeatherGenerator/.venv`
- kernel spec: `/work/bk1444/hackathon_2026/challenge_3/kernel/share/jupyter/kernels/hackathon-downscaling`

Register the shared kernel once for your user (writes a per-user kernelspec pointing at
the shared env — it does **not** copy or rebuild it):

```bash
/work/bk1444/hackathon_2026/challenge_3/WeatherGenerator/.venv/bin/python -m ipykernel install \
  --user --name hackathon-downscaling --display-name "Python (hackathon-downscaling)"
```

**In VS Code**, you can instead select the interpreter directly (top-right *Select Kernel* →
*Select Another Kernel* → *Python Environments*):

```
/work/bk1444/hackathon_2026/challenge_3/WeatherGenerator/.venv/bin/python
```

### Build your own environment (optional)

If you prefer your own copy, install WeatherGenerator with `uv` (Python 3.12) — it contains
everything the notebooks need too:

```bash
git clone https://github.com/ecmwf/WeatherGenerator.git
cd WeatherGenerator
./scripts/actions.sh sync                  # builds .venv via uv
.venv/bin/python -m ipykernel install --user --name hackathon-downscaling
```

Then select the **hackathon-downscaling** kernel in the notebook (top-right
*Select Kernel* button).

## Data

All paths are set in one config cell (`DATA_ROOT` + dataset names), so switching later is a one-line change.
The two zarr datasets live in the `data/` subdirectory of the shared challenge dir:

- **Coarse source (O96, ~1°):**
  `${DATA_ROOT}/aifs-ea-an-oper-0001-mars-o96-1979-2023-6h-v8.zarr`
  — 101 variables, 6-hourly, 1979–2023, O96 octahedral reduced Gaussian grid (40 320 points).

- **Fine target (N320, ~0.25°):**
  `${DATA_ROOT}/aifs-ea-an-oper-0001-mars-n320-1979-2023-6h-v8.zarr`
  — same variables/dates, reduced Gaussian grid with 640 latitude rings and 542 080 points.

`DATA_ROOT` resolves automatically to whichever location holds the datasets: the
`~/data_c3` link created by the workshop setup, otherwise
`/work/bk1444/hackathon_2026/challenge_3/data`. Override it via the `DATA_ROOT`
environment variable (the path may change completely on a different HPC).

> Notebook 1 opens the 1° (O96) ERA5 dataset for the data tour; the downscaling
> baseline (notebook 2) uses the ERA5 O96/N320 pair above.
