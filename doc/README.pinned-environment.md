# Pinned environment for the `new-manifold-plot` branch

`requirements-lock.txt` (repository root) pins every package needed to run
this branch, its tests (`[test]` extra) and the luigi-based figure pipelines
(`[pipeline]` extra). It was resolved with
[uv](https://docs.astral.sh/uv/) from `setup.cfg`, using only package releases
from before 2023-09-23 (`--exclude-newer`), which is the day after the last
commit on this branch. So no dependency is newer than what was available when
the branch (and the "Charting the Realms" paper analysis) was last worked on.

Key versions:

| package           | version |
|-------------------|---------|
| python            | 3.10    |
| torch             | 2.0.1   |
| torchvision       | 0.15.2  |
| pytorch-lightning | 1.9.5   |
| torchmetrics      | 1.2.0   |
| kornia            | 0.7.0   |
| numpy             | 1.26.0  |
| scipy             | 1.11.2  |
| scikit-learn      | 1.3.1   |
| xarray            | 2023.8.0|
| pandas            | 2.1.1   |
| matplotlib        | 3.8.0   |
| luigi             | 3.3.0   |

The lock is "universal" (platform markers are included), so it can be used on
Linux, macOS and Windows. On Linux x86_64 it installs the CUDA 11.7 builds of
torch from PyPI, which also run on CPU-only machines.

## Creating the environment

```bash
uv venv --python 3.10 .venv
uv pip sync --python .venv/bin/python requirements-lock.txt
uv pip install --python .venv/bin/python --no-deps -e .
```

(`--no-deps` makes sure that installing `convml_tt` itself doesn't change any
of the pinned versions.)

Plain `pip` works too:

```bash
python3.10 -m venv .venv
.venv/bin/pip install -r requirements-lock.txt
.venv/bin/pip install --no-deps -e .
```

## Regenerating the lock

The command is in the header of `requirements-lock.txt`. Keep
`--exclude-newer` unless the aim is to upgrade dependencies.

## Notes

- The test suite downloads its example datasets and the pretrained model from
  `homepages.see.leeds.ac.uk/~earlcd/ml-datasets`. That server answered every
  request with HTTP 502 when this lock was made (2026-11-26), so the tests can
  only pass once those files are hosted somewhere else (see
  `convml_tt/data/examples.py`).
- Models trained with fastai v1 (e.g. `fixednorm-stage-2.torch.pkl`) are
  pickled whole modules and are loaded with `torch.load(path)`. This relies
  on `torch.load` defaulting to `weights_only=False`, which changed in torch
  2.6. So keep torch < 2.6 (pinned here at 2.0.1) to load them.
