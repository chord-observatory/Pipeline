# CHORD Analysis Pipeline

This is the overview project for the CHORD offline pipeline for data analysis
and simulation. It holds the guidelines that all CHORD analysis software
follows, and is the place to track pipeline-wide
[issues](../../issues) and plans. It is modelled on CHIME's
[Pipeline](https://github.com/chime-experiment/Pipeline) project, adapted to
CHORD.

### Index

- [Contributing to the CHORD pipeline software](CONTRIBUTING.md): workflow,
  reviews, coding rules, testing, commit messages, dependencies
- [Versioning and releases](VERSIONING.md)
- [Recommended tools](RECOMMENDED_TOOLS.md)
- [Setting up a new repository](NEW_REPOSITORY.md), with ready-to-copy
  [templates](templates/)

### A list of relevant repositories

#### CHORD
- [chord_pipeline](https://github.com/chord-observatory/chord_pipeline):
  CHORD pipeline tasks, containers and telescope models
- [chord_util](https://github.com/chord-observatory/chord_util) (private):
  general CHORD utilities, including `andata` for reading the X-engine data
- [kotekan](https://github.com/kotekan/kotekan) (branch `chord`): the real-time
  X-engine and receiver software, which writes the visibility files the
  pipeline reads. Its `docs/sphinx/user/file_formats/` documents the file format.

#### Shared with CHIME (radiocosmology)
- [caput](https://github.com/radiocosmology/caput): cluster astronomical Python
  utilities, including the pipeline framework (`caput.pipeline`) and containers
  ([docs](https://caput.readthedocs.io/))
- [draco](https://github.com/radiocosmology/draco): pipeline tasks and containers
  for the analysis and simulation of drift scan radio data
  ([docs](https://radiocosmology.github.io/draco))
- [driftscan](https://github.com/radiocosmology/driftscan): transit
  interferometer modelling (beam transfer matrices, KL, ...), including the dish
  array telescopes used for CHORD ([docs](https://radiocosmology.github.io/driftscan))
- [cora](https://github.com/radiocosmology/cora): simulated skies (21cm,
  galactic and extragalactic foregrounds) ([docs](https://radiocosmology.github.io/cora/))
- [fluxcat](https://github.com/radiocosmology/fluxcat): catalogs of the measured
  flux densities of astronomical sources

Changes that are not CHORD specific belong upstream in these repositories, not
in CHORD forks. Please add any CHORD repository that is missing from this list.

#### CHIME repositories without a CHORD counterpart (yet)
- `ch_ephem` (observer location and ephemeris): the CHORD location currently
  lives in `chord_pipeline.core.telescope`
- `chimedb.*` (data index, flags, datasets): CHORD has no database yet
- `mkchimeenv` (environment setup): CHORD uses `mkvenv.sh` in chord_pipeline
- `RTPipeline`, `Simulations`, `Analysis` (tracking projects)
