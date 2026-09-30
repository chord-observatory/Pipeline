<!---
Parts of this document (the commit message conventions) are based on
https://github.com/angular/angular/blob/main/CONTRIBUTING.md, via CHIME's
https://github.com/chime-experiment/Pipeline/blob/main/CONTRIBUTING.md,
and are distributed under the...

The MIT License

Copyright (c) 2010-2019 Google LLC. http://angular.io/license

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
-->
# Contributing to the CHORD Pipeline Software

- [Workflow](#workflow)
- [Pull requests and reviews](#reviews)
- [Coding rules](#rules)
- [Testing](#testing)
- [Commit messages and pull request titles](#commit)
- [Dependencies](#dependencies)
- [Data, notebooks and credentials](#data)
- [Documentation](#docs)

These rules apply to every repository in the
[list on the index page](README.md#a-list-of-relevant-repositories) that is
owned by CHORD. For the shared radiocosmology repositories (caput, draco,
driftscan, cora, fluxcat) follow their own guidelines, which are the same as
CHIME's.

## <a name="workflow"></a> Workflow

We use [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow):
`main` always works, and every change is made on a short-lived branch and merged
through a pull request (PR).

1. Look for an existing issue or PR about your change, so you don't duplicate
   effort. For anything larger than a small fix, open an issue first describing
   the problem or the design, and discuss it before writing the code.
2. Create a branch from an up-to-date `main`, named with your initials and a
   short description:

    ```shell
    git fetch origin
    git checkout -b ab/short-description origin/main
    ```

3. Make your change, **including tests** and documentation (see below). Keep a
   PR to one feature or fix; split large work into a series of PRs.
4. Run the checks locally before pushing (`pre-commit` runs the first two for
   you, see [Recommended tools](RECOMMENDED_TOOLS.md)):

    ```shell
    ruff format .
    ruff check .
    python -m pytest
    ```

5. Commit in logical steps (don't mix unrelated changes in one commit), push the
   branch and open a PR to `main`. Opening a **draft PR** early is encouraged to
   get feedback.

    ```shell
    git push -u origin ab/short-description
    ```

6. Address review comments with new commits (don't force-push while a review is
   in progress, so reviewers can see what changed). If `main` moves on, update
   your branch with `git merge origin/main` (or rebase, if nobody else has used
   the branch).

Don't use long-lived, person-specific branches (e.g. `ac-dev`), and never
commit directly to `main` (it is protected, see below).

### After your pull request is merged

Delete the branch (GitHub can do this automatically) and update your `main`:

```shell
git checkout main
git pull --ff-only origin main
git branch -d ab/short-description
```

## <a name="reviews"></a> Pull requests and reviews

A PR can be merged when:

- the automated checks (linting, tests, docs build) pass;
- it has been approved by at least one **code owner** of the files it touches
  (GitHub requests them automatically from the repository's `CODEOWNERS` file).
  Changes to core interfaces (containers, file formats, the pipeline task API)
  need a second approval from someone working in the affected area;
- all review conversations are resolved.

These are enforced with branch protection on `main` (see
[Setting up a new repository](NEW_REPOSITORY.md)).

Reviewer groups are kept as GitHub teams in the `chord-observatory`
organisation, and each repository maps its directories to them in
`.github/CODEOWNERS`, so the list of who reviews what lives in one place and
can't go stale in a document. Anyone who uses or contributes to the pipeline
code should belong to at least one reviewer team. If you are asked to review
code you don't know yet, take it as an opportunity to learn it; if you feel out
of your depth, say so on the PR.

**Merging.** Once approved, the author merges the PR using **squash and merge**,
so that `main` gets one commit per PR, whose message is the PR title and
description. The PR title must therefore follow the
[convention below](#commit).

As a reviewer, check that the change does what it says, is tested, is
documented, and doesn't break other code (search the other CHORD repositories
for uses of anything whose behaviour changes).

## <a name="rules"></a> Coding rules

- All features and bug fixes **must be tested** (see [Testing](#testing)).
- All public modules, classes, functions and methods **must be documented**,
  with [NumPy style docstrings](https://numpydoc.readthedocs.io/en/latest/format.html)
  (`Parameters`, `Returns`, `Raises`, `Examples`, ...). Explain *why* in
  comments where the code isn't self-explanatory.
- Follow the [PEP 8](https://peps.python.org/pep-0008/) style guide. You
  don't need to apply it by hand: `ruff format` lays out the code
  (black-compatible, i.e. PEP 8 with lines of up to 88 characters), and
  `ruff check` checks the rest, including PEP 8's naming conventions (the
  pycodestyle `E`/`W` and pep8-naming `N` rules), using the settings in the
  repository's `pyproject.toml`. The CI checks both.   
- Pipeline code should be written as `caput.pipeline` tasks, and data passed
  between tasks in `caput`/`draco` containers, so that tasks from the different
  repositories work together.
- Code that runs under MPI must call collective operations on all ranks (e.g.
  don't read a distributed file on rank 0 only), and should work for any number
  of ranks, including one.
- Don't hard-code paths to data, cluster directories or user directories in
  library code. Take them as task parameters or config values.
- Use `logging` (e.g. the task's `self.log`), not `print`.
- Put code in the lowest-level repository where it belongs: generic
  radio-astronomy code in caput/draco/driftscan, CHORD-specific utilities in
  chord_util, and pipeline tasks and containers in chord_pipeline.

## <a name="testing"></a> Testing

- Use [pytest](https://docs.pytest.org/). Tests live in `tests/` and must run
  with a plain `python -m pytest`, in a few minutes at most.
- Tests may not depend on data that only exists on a particular cluster.
  You can generate small synthetic inputs in the tests instead (see chord_util's
  `tests/xengine_testdata.py` for an example that writes fake X-engine files
  with a known answer). Make the synthetic data independently of the code it
  tests, so that a bug in that code can't hide in the test data.
- Checks against real data are welcome in addition, but must be skipped
  automatically where the data isn't readable (e.g. with
  `pytest.mark.skipif`), with the data location configurable through an
  environment variable.
- Code that uses MPI should be tested under MPI too
  (`mpirun -np 3 python -m pytest`). Use a temporary directory shared by all
  ranks for anything written in parallel.
- A bug fix should come with a test that fails without the fix.

## <a name="commit"></a> Commit messages and pull request titles

We use the [conventional commit](https://www.conventionalcommits.org/) format
(from [Angular](https://github.com/angular/angular)) for **PR titles**, which
become the commit messages on `main` when a PR is squash-merged and the entries
of the automatically generated release notes (see [Versioning](VERSIONING.md)).
Commits within a PR should follow it too, but are not checked.

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

The **type** and **subject** are mandatory; the **scope**, **body** and
**footer** are optional. Keep lines under 100 characters.

Examples:

```
feat(io): add LoadCorrDataFiles task for X-engine data
```
```
fix(andata): drop unfilled time bins at the end of an acquisition

The last file of an acquisition can end with unfilled bins, which were read as
zeros with time 0.

Closes #12
```

### Type

- **feat**: a new feature
- **fix**: a bug fix
- **perf**: a change that improves performance
- **refactor**: a change that neither fixes a bug nor adds a feature
- **docs**: documentation only
- **test**: adding or correcting tests
- **build**: the build system or dependencies (e.g. `pyproject.toml`, requirements)
- **ci**: the CI configuration
- **style**: formatting only (white space, ...)
- **revert**: reverts a previous commit; the body says `This reverts commit <hash>.`

### Scope

The name of the module or area changed, e.g. `andata`, `io`, `containers`,
`telescope`.

### Subject

- use the imperative, present tense: "change", not "changed" or "changes"
- don't capitalise the first letter
- no full stop at the end

### Body

The motivation for the change, and how it differs from the previous behaviour.

### Footer

**Breaking changes** start with `BREAKING CHANGE:` and explain what breaks and
how to adapt. A change is breaking if it changes a function signature or
behaviour, a config parameter, a dependency, or the structure of a file that is
read or written. In the PR title, mark it with a `!` after the type/scope, e.g.
`feat(containers)!: rename the flags group`.

**Issues** fixed by the change are referenced as `Closes #123` (or
`Closes #123, #245`).

## <a name="dependencies"></a> Dependencies

- Dependencies are declared in the repository's `pyproject.toml`. Use this
  format for dependencies on GitHub repositories:

    ```
    # public repository
    package @ git+https://github.com/organisation/package.git
    # private repository
    package @ git+ssh://git@github.com/organisation/package.git
    ```

  Only use `ssh` for private repositories: it fails for anyone (and any CI job)
  without SSH access to GitHub.
- A public repository should not depend on a private one, otherwise nobody
  outside the organisation (and no CI job without a secret key) can install it.
- Don't depend on personal forks or feature branches in `main`; get the change
  merged upstream instead.
- Processing runs whose outputs are used for science must be reproducible: run
  them from released versions or exact commits of every CHORD repository, and
  record the versions in the outputs (e.g. with `save_versions` in the pipeline
  config).

## <a name="data"></a> Data, notebooks and credentials

- Don't commit data files. Test inputs should be generated by the tests or be
  small (well under 1 MB).
- Don't commit notebooks to the package repositories. Where notebooks are
  shared (e.g. tutorials), strip their outputs first (`nbstripout`).
- Never commit passwords, tokens or keys. Use environment variables or
  configuration files kept out of the repository.

## <a name="docs"></a> Documentation

- Each repository has a README with what it is for, how to install it, how to
  run the tests, and links to these guidelines and to its documentation.
- Reference documentation is built from the docstrings with
  [Sphinx](https://www.sphinx-doc.org/) and published with GitHub Pages; the CI
  builds it on every PR so broken docstrings are caught early.
- Changes to behaviour or configuration must update the documentation in the
  same PR.
