# Versioning and releases

## Version numbering

We use the same calendar-based scheme as CHIME and the shared radiocosmology
repositories, `major.minor.patch`:

- **major**: the last two digits of the year
- **minor**: the number of the month (January is 1)
- **patch**: zero for the first release in a month, incremented for each further
  release that month

For example, the first release in May 2026 is `26.5.0`, the next one that month
`26.5.1`, and the first one in June `26.6.0`.

The version says *when* a release was made, not how much changed. Whether a
release breaks anything is recorded in its release notes (see `BREAKING CHANGE`
in [Contributing](CONTRIBUTING.md#commit)).

## Dynamic versioning in a repository

The version of an installed package is derived from its git tags at install
time with
[setuptools-git-versioning](https://setuptools-git-versioning.readthedocs.io/),
so it never has to be edited by hand. To set it up:

- in `pyproject.toml`:

    ```toml
    [build-system]
    requires = ["setuptools>=61.0.0", "wheel", "setuptools-git-versioning"]
    build-backend = "setuptools.build_meta"

    [project]
    dynamic = ["version"]

    [tool.setuptools-git-versioning]
    enabled = true
    ```

- in the package's top-level `__init__.py`, and nowhere else:

    ```python
    from importlib.metadata import PackageNotFoundError, version

    try:
        __version__ = version("<package-name>")
    except PackageNotFoundError:
        # package is not installed
        pass

    del version, PackageNotFoundError
    ```

`versioneer` (a `versioneer.py` and `_version.py` in the repository) is
deprecated, as it is hard to keep working with new Python versions. Repositories
still using it, or other schemes such as `setuptools-scm`, should move to the
above.

## How to publish a release

Releases are made with GitHub Releases
([documentation](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository)),
from `main`.

1. Work out the version number from the rules above, e.g. `26.9.0`.
2. On the repository's Releases page, click "Draft a new release".
3. Under "Choose a tag", type the new tag, a lower-case `v` followed by the
   version (e.g. `v26.9.0`), and select "Create new tag ... on publish".
4. Set the release title to the version number, without the `v` (e.g. `26.9.0`).
5. Click "Generate release notes". GitHub lists the PRs merged since the last
   release, by their titles, which is why PR titles must follow the
   [convention](CONTRIBUTING.md#commit). Edit the notes if needed, and make any
   breaking changes stand out at the top.
6. Leave "Set as a pre-release" unchecked and "Set as the latest release"
   checked, then click "Publish release".

Make a release whenever `main` has changes that are used for processing, so
that processing runs can refer to a release rather than an arbitrary commit.
