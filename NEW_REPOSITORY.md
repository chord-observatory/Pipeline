# Setting up a new repository

A checklist for new CHORD repositories (and for bringing existing ones up to
date). The files referred to are in [templates/](templates/).

### On GitHub

- [ ] Create it in the `chord-observatory` organisation, named in lower case
      with underscores, like the Python package (e.g. `chord_util`).
- [ ] Make it **public** unless there is a reason not to. A private repository
      can't be installed by anyone outside the organisation or by CI without a
      secret key, and no public repository may depend on it.
- [ ] Protect `main` (Settings → Branches, or Rules → Rulesets):
  - require a pull request before merging, with at least 1 approval and
    review from code owners;
  - dismiss stale approvals when new commits are pushed;
  - require the CI checks to pass and branches to be up to date;
  - require conversations to be resolved;
  - no force pushes or deletion.
- [ ] Under Settings → General → Pull Requests, allow only **squash merging**,
      with the PR title and description as the default commit message, and
      tick "Automatically delete head branches".
- [ ] Give the reviewer teams access, and add a `.github/CODEOWNERS`
      ([template](templates/github/CODEOWNERS)).
- [ ] Add the PR template as `.github/pull_request_template.md`
      ([template](templates/github/pull_request_template.md)).
- [ ] Enable GitHub Pages from the `gh-pages` branch, for the documentation.
- [ ] Add the repository to the [list on the index page](README.md#a-list-of-relevant-repositories).

### In the repository

- [ ] `LICENSE` (MIT, "CHORD Collaboration").
- [ ] `README.md`: what the package is for, installation, how to run the
      tests, a link to the documentation and to these guidelines.
- [ ] `pyproject.toml` ([template](templates/pyproject.toml)), with
      setuptools-git-versioning (see [Versioning](VERSIONING.md)) and the ruff
      settings. No `setup.py`, `setup.cfg` or `versioneer.py`.
- [ ] `.pre-commit-config.yaml` ([template](templates/pre-commit-config.yaml)).
- [ ] `.gitignore` covering `__pycache__/`, `*.egg-info/`, `build/`, `dist/` and
      the docs build directory.
- [ ] `tests/`, runnable with `python -m pytest` and without real data.
- [ ] `.github/workflows/ci.yaml` ([template](templates/github/workflows/ci.yaml)).
- [ ] `doc/` with a Sphinx setup (see ch_util or ch_pipeline for an example).
- [ ] `CITATION.cff` ([template](templates/CITATION.cff)).

### Private dependencies in CI

If a repository (necessarily a private one) depends on another private
repository, its CI needs read access to it: add a deploy key or a fine-grained
token with read access as an organisation secret, and use it in the workflow
(see the comment in the CI template).
