# Requirements

## Recommended Path (Docker)

- Docker Engine >= 20.10
  - Installation: https://docs.docker.com/get-started/
- No other software dependencies are required when using Docker

## Alternative Path (Building from Source)

Building from source requires Ubuntu 22.04 (or a compatible Linux
distribution). The specific steps to set up the environment are explained in
[INSTALL.md](INSTALL.md). The following is a summary of the software
dependencies, per project.

**P4-SpecTec** (required for RQ1, RQ2, RQ3):
- System packages: `pkg-config`, `libgmp-dev`, `opam`
- opam >= 2.0 (OCaml package manager)
- OCaml 5.1.0, installed via `opam switch create 5.1.0`
- OCaml libraries (installed via opam): `dune`, `bignum`,
  `menhir=20240715`, `menhirLib=20240715`, `core`, `core_unix`,
  `bisect_ppx`, `yojson`, `ppx_deriving_yojson`

**Petr4** (required for RQ3):
- System packages: `pkg-config`, `m4`, `libgmp-dev`, `opam`,
  `ca-certificates`, `curl`, `unzip`
- opam (local switch at project root; package versions pinned via
  `petr4-013.export`)

**HOL4P4** (required for RQ3):
- Installed automatically by `hol4p4/scripts/install.sh`, which pulls in:
  - System packages: `build-essential`, `git`, `python3`, `file`,
    `zlib1g-dev`, `libbz2-dev`, `liblzma-dev`, `wget`
  - PolyML, HOL4, opam/OCaml, Ott, and its own bundled Petr4 build

**Reproduction scripts** (RQ1, RQ2, RQ3 driver/table scripts under `scripts/`):
- Python 3
- Python package: `tqdm` — install with `python3 -m pip install tqdm`
  (or `apt install python3-tqdm`)

## Time Requirements

- Kick-the-Tires: ~5 minutes
- Reproducing RQ1 (`scripts/rq1`, all test suites): ~20 minutes on a 2020
  MacBook Air (Apple M1, 16GB RAM); the `V1Model` and `V1Model+` suites
  account for most of the runtime
- Reproducing RQ2 (`scripts/rq2-table5`, `scripts/rq2`): up to 2 hours,
  depending on the test suite (`V1Model+` and `IL-det`/`IL` are slow)
- Reproducing RQ3 (`scripts/rq3`): up to 12 hours, depending on the test suite;
  we provide the raw comparison results in `logs/rq3-pre`
