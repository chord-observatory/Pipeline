# Recommended tools for CHORD pipeline development

### Formatting and linting

- [ruff](https://docs.astral.sh/ruff/): `ruff format` formats the code (replacing
  `black`) and `ruff check` checks style, docstrings and common bugs. Configure
  it in `pyproject.toml` (see [templates/pyproject.toml](templates/pyproject.toml)).
- [pre-commit](https://pre-commit.com/): runs `ruff` automatically on every
  commit. Install it once per clone with `pre-commit install`, using
  [templates/pre-commit-config.yaml](templates/pre-commit-config.yaml) as the
  repository's `.pre-commit-config.yaml`.
- [yamllint](https://github.com/adrienverge/yamllint) for YAML files, e.g.
  pipeline configs.

### Testing

- [pytest](https://docs.pytest.org/) for all tests.
- [pytest-mpi](https://pytest-mpi.readthedocs.io/) to mark and run tests under
  MPI (`mpirun -np 3 python -m pytest --with-mpi`).
- [pytest-cov](https://pytest-cov.readthedocs.io/) to see which code the tests
  cover (`python -m pytest --cov`).

### Continuous integration

[GitHub Actions](https://docs.github.com/en/actions), running on every PR and on
`main`: linting, the tests (serially and under MPI), and the documentation
build. See [templates/github/workflows/ci.yaml](templates/github/workflows/ci.yaml).

### Documentation

- [Sphinx](https://www.sphinx-doc.org/) with the `napoleon` extension (NumPy
  docstrings), `autosummary` and `intersphinx` (to link to the caput, draco and
  numpy docs).
- [GitHub Pages](https://pages.github.com/) to publish them, built by CI on
  pushes to `main`. 

### Command line applications

[click](https://click.palletsprojects.com/). 

### Notebooks

[nbstripout](https://github.com/kynan/nbstripout) removes outputs before
notebooks are committed (`nbstripout --install` once per clone).

### Citation

A [`CITATION.cff`](https://citation-file-format.github.io/) file in each
repository, and [Zenodo](https://zenodo.org/) to give releases a DOI.
