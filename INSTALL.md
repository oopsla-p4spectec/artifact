# Installation Guide

We provide three ways to install the artifact: pulling a pre-built image from
DockerHub, building the Docker image from the provided `Dockerfile`, or
installing from source on an Ubuntu 22.04 system. We recommend pulling the
pre-built image from DockerHub for the quickest setup. See
[REQUIREMENTS.md](REQUIREMENTS.md) for the full software/hardware/time
breakdown.

For Option B and Option C, you will need to clone the repository and setup
submodules first.

```bash
$ git clone https://github.com/oopsla-p4spectec/artifact
$ git submodule update --init --recursive
```

## Option A: Pull from DockerHub (Recommended)

The pre-built image is published at
[oopslap4spectec/p4spectec-artifact](https://hub.docker.com/repository/docker/oopslap4spectec/p4spectec-artifact/general).
No need to clone the repository for this option.

### Step 1: Pull the image

```bash
$ docker pull oopslap4spectec/p4spectec-artifact:latest
```

### Step 2: Start the container

Run the docker container, and execute `bash` inside it:

```bash
# launch a docker container in detached state.
$ docker run -dit --name artifact oopslap4spectec/p4spectec-artifact:latest

# run a shell inside the container
$ docker exec -it artifact bash
```

Proceed to [Validating the Installation](#validating-the-installation) below.

## Option B: Build the Docker Image from Source

Make sure you have cloned the repository and setup submodules as described above.

### Step 1: Build the image

Build an image from the `Dockerfile` provided by this repository.

```bash
$ docker build -t p4spectec-artifact:latest .
```

### Step 2: Start the container

Run the docker container, and execute `bash` inside it:

```bash
# launch a docker container in detached state.
$ docker run -dit --name artifact p4spectec-artifact

# run a shell inside the container
$ docker exec -it artifact bash
```

Proceed to [Validating the Installation](#validating-the-installation) below.

## Option C: Installing from Source on Ubuntu 22.04

Make sure you have cloned the repository and setup submodules as described above.

The artifact consists of three projects: P4-SpecTec (ours), and HOL4P4/Petr4
for comparison. HOL4P4 and Petr4 are necessary for reproducing RQ3, but not for
RQ1 and RQ2. P4-SpecTec is necessary for reproducing all RQ1, RQ2, and RQ3.

### Step 1: Installing P4-SpecTec

Navigate to the P4-Spectec project directory, and install the following dependencies:

```bash
$ cd p4-spectec
$ sudo apt update && \
  sudo apt install -y --no-install-recommends \
    pkg-config \
    libgmp-dev \
    opam
```

Initialize opam if it has not been yet:

```bash
$ opam init --disable-sandboxing -y
```

Create an opam switch for ocaml 5.1.0, and install opam dependencies:

```bash
$ opam switch create 5.1.0
$ eval $(opam env --switch=5.1.0)

$ opam install dune bignum 'menhir=20240715' 'menhirLib=20240715' core core_unix bisect_ppx yojson ppx_deriving_yojson -y
$ eval $(opam env)
```

Build the project, which will produce `p4spectec`, `p4spectec-test`,
`p4spectec-stat`, and `p4spectec-perf` binaries under the project root
directory.

```bash
$ make release spec-test stat perf
```

### Step 2: Installing HOL4P4

Navigate to the HOL4P4 project directory, and run the install script to install
HOL4P4 and its dependencies.

```bash
$ cd hol4p4
$ ./scripts/install.sh
```

### Step 3: Installing Petr4

Navigate to the project directory and install the following dependencies:

```bash
$ cd petr4
$ sudo apt update && \
  sudo apt install -y --no-install-recommends \
    pkg-config \
    m4 \
    libgmp-dev \
    opam \
    ca-certificates \
    curl \
    unzip
```

Initialize opam if it has not been yet:

```bash
$ opam init --disable-sandboxing -y
```

Create a local switch at project root, and install opam depdencies.

```bash
$ opam switch create . --empty
$ eval $(opam env)

# petr4-013.export is a list of opam packages with their versions pinned.
$ opam switch import petr4-013.export -y
$ eval $(opam env)
```

Build and install petr4.

```bash
$ dune build
$ dune install
```

### Step 4: Installing Python dependencies

The `scripts/` used to reproduce RQ1-RQ3 (see [README.md](README.md)) require
Python 3 and the `tqdm` package.

```bash
$ sudo apt install -y python3-tqdm
# or: python3 -m pip install tqdm
```

Proceed to [Validating the Installation](#validating-the-installation) below.

## Validating the Installation

If you installed via Docker (Option A or B), the following directories should
be available at the root (`/`) of the container: `p4-spectec`, `HOL4P4`,
`petr4`, `testdata`, `p4include`, `logs`, and `scripts`. If you installed from
source (Option C), the equivalent directories (`hol4p4` instead of `HOL4P4`)
are available under the cloned repository root instead.

From the P4-SpecTec project directory (`/p4-spectec` in the Docker image, or
`p4-spectec/` if installed from source), confirm the built binaries are
present and runnable:

```bash
# in the Docker image, first navigate to the project directory
$ cd /p4-spectec

$ ./p4spectec --help
$ ./p4spectec-test --help
```

If you installed HOL4P4 and/or Petr4 (required for RQ3), confirm their builds
succeeded without errors as reported by `scripts/install.sh` (HOL4P4) and
`dune build`/`dune install` (Petr4) above.

See [README.md](README.md) for instructions on reproducing RQ1, RQ2, and RQ3.
