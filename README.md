# Artifact for "P4-SpecTec: Integrating a Language Mechanization Framework into the Real-World P4 Specification"

## Organization

In this README, we explain how to:

1. [Install P4-SpecTec and the Artifact](#1-installation)
2. [Organization](#2-organization)
3. [Use the P4-SpecTec Framework](#3-using-the-p4-spectec-framework)
4. [Reproduce RQ1, RQ2, and RQ3](#4-reproducing-rq1-rq2-and-rq3)
    * [RQ1: Adequacy of Algorithmic Inference Rules](#rq1-adequacy-of-algorithmic-inference-rules)
    * [RQ2: Effectiveness of Structuring](#rq2-effectiveness-of-structuring)
    * [RQ3: Comparison with Existing Formalizations](#rq3-comparison-with-existing-formalizations)

## 1. Installation

See [REQUIREMENTS.md](REQUIREMENTS.md) for software, hardware, and time
requirements, and [INSTALL.md](INSTALL.md) for step-by-step installation
instructions. We offer three installation options: pulling a pre-built image
from DockerHub (recommended), building the Docker image from source, or
installing from source on Ubuntu 22.04.

## 2. Organization

This artifact repository is organized as follows:

```
/
├── p4-spectec/    # P4-SpecTec submodule (framework, mechanized spec, binary)
│   ├── p4spec/    # OCaml implementation of the P4-SpecTec framework
│   ├── spec/      # Mechanized P4 specification
│   └── docs/      # Generated HTML/PDF specifications (RQ1.B)
├── petr4/         # Petr4 project, used for comparison in RQ3
├── hol4p4/        # HOL4P4 project, used for comparison in RQ3
├── p4include/     # P4 library headers used by test programs
├── testdata/      # Test suites (p4c, p4testgen), excludes, and patches
├── scripts/       # rq1, rq2, rq3 reproduction scripts
└── logs/          # Output logs and results from running the scripts
```

## 3. Using the P4-SpecTec Framework

We demonstrate how to parse the mechanized spec to EL, elaborate it to IL,
structure it to SL, and generate PL for prose generation.

After installation, a `p4spectec` binary should be available in the P4-SpecTec
submodule root.

### Parsing and Elaboration

To parse and elaborate the mechanized spec, run the following command in the
P4-SpecTec submodule root:

```bash
$ ./p4spectec elab spec/*/*.watsup
```

This will output the parsed and elaborated specification to stdout.

To execute the elaborated IL specification, run:

```bash
$ ./p4spectec run spec/*/*.watsup -rel Program_ok \
   -i ../p4include -p ../testdata/p4_16_samples/action-bind.p4 -il
```

This executes the relation `Program_ok`, on the P4 program
`../testdata/p4c/p4_16_samples/action-bind.p4`, where the include path is
`../p4include`. The `-il` flag indicates that we run the IL interpreter.

To execute the elaborated IL specification for the P4 dynamic semantics, run:

```bash
./p4spectec sim spec/*/*.watsup -a v1model \
   -i ../p4include -p ../testdata/p4c/v1model/arith-bmv2.p4 \
   -stf ../testdata/p4c/v1model/arith-bmv2.stf -il
```

This runs the V1Model simulator on the P4 program
`../testdata/p4c/v1model/arith-bmv2.p4`, with the include path `../p4include`,
and the STF file `../testdata/p4c/v1model/arith-bmv2.stf`. The `-il` flag
indicates that we run the IL interpreter.

In general, you may run:

```bash
./p4spectec sim spec/*/*.watsup -a {v1model|ebpf|psa} \
   -i ../p4include -p ../testdata/p4c/{v1model|ebpf|psa}/[NAME].p4 \
   -stf ../testdata/p4c/{v1model|ebpf|psa}/[NAME].stf -il
```

For the above commands, you may pass the `-det` flag to enable determinism validation.

### Structuring

To structure the mechanized spec, run the following command in the P4-SpecTec
submodule root:

```bash
$ ./p4spectec struct spec/*/*.watsup
```

This will output the structured specification to stdout.

SL interpreter can be similarly executed as the IL interpreter, by replacing
the `-il` flag with `-sl`.

```bash
$ ./p4spectec run spec/*/*.watsup -rel Program_ok \
   -i ../p4include -p ../testdata/p4_16_samples/action-bind.p4 -sl
```

```bash
./p4spectec sim spec/*/*.watsup -a v1model \
   -i ../p4include -p ../testdata/p4c/v1model/arith-bmv2.p4 \
   -stf ../testdata/p4c/v1model/arith-bmv2.stf -sl
```

```bash
./p4spectec sim spec/*/*.watsup -a {v1model|ebpf|psa} \
   -i ../p4include -p ../testdata/p4c/{v1model|ebpf|psa}/[NAME].p4 \
   -stf ../testdata/p4c/{v1model|ebpf|psa}/[NAME].stf -sl
```

### Prose Generation

To generate the prose specification, run the following command in the
P4-SpecTec submodule root:

```bash
$ ./p4spectec prose spec/*/*.watsup
```

This will output the generated prose specification, in AsciiDoc format, to
stdout.

## 4. Reproducing RQ1, RQ2, and RQ3

We provide scripts that wrap the commands above to reproduce the results in
RQ1, RQ2, and RQ3. The scripts are located in the `scripts/` directory.

### RQ1: Adequacy of Algorithmic Inference Rules

#### A. Executable backend tests

##### Experimental Setup (Reproducing Table 2)

We run tests on positive and negative type checker tests for the type checker backend:

| Suite | Count | Description |
| --- | --- | --- |
| `Pos` | 1,307 | Positive type checker tests from the `p4c` repository. |
| `Neg` | 564 | Negative type checker tests from the `p4c` repository. |

For the interpreter backend, we run tests on five test suites spanning three
architectures: V1Model, eBPF, and PSA. The test suites are labeled as follows:

| Suite | Count | Description |
| --- | --- | --- |
| `V1Model` | 203 | P4-STF pairs for the V1Model architecture from the `p4c` repository. |
| `eBPF` | 17 | P4-STF pairs for the eBPF architecture from the `p4c` repository. |
| `V1Model+` | 1,982 | Additional P4–STF pairs for the V1Model architecture, generated by feeding P4 programs from `p4c` into P4Testgen, configured to produce up to 10 STF files per program. |
| `eBPF+` | 144 | Additional P4–STF pairs for the eBPF architecture, generated by feeding P4 programs from `p4c` into P4Testgen, configured to produce up to 10 STF files per program. |
| `PSA` | 26 | P4-STF pairs for the PSA architecture from the `p4c` repository. |

The excluded tests are listed in `testdata/excludes`, and the two patched STF
tests are listed in `testdata/patches`.

Run `scripts/rq1-table2` to render Table 2 (categorization of excluded tests)
from the paper, reading directly from `testdata/excludes`:

```bash
$ scripts/rq1-table2 -e testdata/excludes
```

##### Running the Tests (Reproducing Tables 3 and 4)

Execute the following command to run the P4-SpecTec P4 type checker and
interpreter backend tests, on the SL interpreter with `-det` mode.

```bash
$ scripts/rq1 -i p4include -e testdata/excludes -p testdata/patches \
   -t testdata -l logs --spectec p4-spectec
```

By default, `scripts/rq1` runs all test suites. Pass `-s` with one or more of
`POS`, `NEG`, `V1Model`, `V1Model+`, `eBPF`, `eBPF+`, `PSA` to run only a
subset. For example, to run only the `V1Model` and `V1Model+` suites:

```bash
$ scripts/rq1 -i p4include -e testdata/excludes -p testdata/patches \
   -t testdata -l logs --spectec p4-spectec -s V1Model V1Model+
```

Running all test suites takes about 20 minutes on a 2020 MacBook Air with an
Apple M1 chip with 16GB of RAM. `V1Model` and `V1Model+` suites account for the
majority of the runtime.

Check that the console output prints a pass/fail/exclude summary for each test
type, and that the per-run JSON results are written under `logs/`.

After `scripts/rq1` has produced JSON logs under `logs/`, run
`scripts/rq1-table3` and `scripts/rq1-table4` to render Table 3 (P4 type
checker backend results) and Table 4 (P4 interpreter backend results) from the
paper. Each reads the most recent matching JSON log per suite from `logs/rq1/`.

```bash
$ scripts/rq1-table3 -l logs
$ scripts/rq1-table4 -l logs -e testdata/excludes -p testdata/patches
```

#### B. Document backend

The generated HTML and PDF specifications can be found in
`docs/P4-16-spec-release.html` and `docs/P4-16-spec-release.pdf` in the
P4-SpecTec submodule.

### RQ2: Effectiveness of Structuring (Reproducing Table 5 and Fig. 16)

Execute the following command to collect structuring statistics and print Table
5 (Comparison of EL, IL, and SL) in the paper.

```bash
$ scripts/rq2-table5 --spectec p4-spectec -l logs
```

Then execute the following command to run performance evaluation on `Pos` and
`V1Model+` tests.

```bash
$ scripts/rq2 -i p4include -e testdata/excludes -p testdata/patches \
   -t testdata -l logs --spectec p4-spectec
```

Check that this prints performance timings for `Pos` and `V1Model+`, with
results and logs written under `logs/`.

By default, `scripts/rq2` runs all suite/variant combinations. Pass `-s` with
one or more of `POS`, `V1Model+` and/or `-v` with one or more of `IL-det`,
`IL`, `SL-det`, `SL` to run only a subset. For example, to run only the `SL`
variant on both suites:

```bash
$ scripts/rq2 -i p4include -e testdata/excludes -p testdata/patches \
   -t testdata -l logs --spectec p4-spectec -v SL
```

After `scripts/rq2` has produced `logs/rq2/time.log`, run `scripts/rq2-fig16`
to render Fig. 16 (Performance comparison of IL and SL interpreters) from the
paper as a table:

```bash
$ scripts/rq2-fig16 -l logs
```

### RQ3: Comparison with Existing Formalizations (Reproducing Table 6)

In this section, we compare P4-SpecTec with Petr4 and HOL4P4. Each project can
be found in the root directory of the docker container.

> [!NOTE]
> The HOL4P4 directory is named `HOL4P4` (uppercase) in the docker container,
> but `hol4p4` (lowercase) if you installed from source. The commands below
> assume the docker container; if you installed from source, replace
> `HOL4P4` with `hol4p4` in the `--hol4p4` argument.

#### Running the Tests

We provide `rq3`, a script to simplify running test suites across projects.
Because a full `scripts/rq3` run is impractically slow (see the warning above),
we bundle a pre-run set of comparison CSVs at `logs/rq3-pre/`, in the same
format `scripts/rq3` itself produces. You may jump to the [Reproducing Table
6](#reproducing-table-6) section to skip running the tests and use the pre-run
CSVs.

##### Static Semantics

We compare the implementation of P4 static semantics from P4-SpecTec and Petr4
by running both positive and negative type checker tests on the two projects.
HOL4P4 is excluded from this comparison, as it is unable to execute its P4 type
system mechanization.

Execute the following command to compare test results from P4-SpecTec and Petr4.

```bash
# Run positive test suite and compare results across projects
$ scripts/rq3 -i p4include -e testdata/excludes/ -t testdata -l logs \
   --spectec p4-spectec --petr4 petr4 --hol4p4 HOL4P4 \
   static compare pos

Generating CSV...
Comparison result written to [...]/positive-static-results.csv.

# Run negative test suite and compare results across projects
$ scripts/rq3 -i p4include -e testdata/excludes/ -t testdata -l logs \
   --spectec p4-spectec --petr4 petr4 --hol4p4 HOL4P4 \
   static compare neg

Generating CSV...
Comparison result written to [...]/negative-static-results.csv.
```

Check that each command produces a CSV comparing pass/fail results between
P4-SpecTec and Petr4, per test program.

Running a test suite with a single project is also possible. For example, the
following command runs the negative typecheck test suite with Petr4.

```bash
$ scripts/rq3 -i p4include -e testdata/excludes/ -t testdata -l logs \
   --spectec p4-spectec --petr4 petr4 --hol4p4 HOL4P4 \
   static petr4 neg
```

##### Dynamic Semantics

We compare the implementation of P4 dynamic semantics from P4-SpecTec, Petr4
and HOL4P4 by running the test suites `V1Model`, `eBPF`, `V1Model+`, and
`eBPF+`.

Execute the following command to compare test results from P4-SpecTec, Petr4
and HOL4P4.

```bash
# Run positive test suite and compare results across projects
$ scripts/rq3 -i p4include -e testdata/excludes/ -t testdata -l logs \
   --spectec p4-spectec --petr4 petr4 --hol4p4 HOL4P4 \
   dynamic compare <v1model|ebpf|v1model+|ebpf+>

Generating CSV...
Comparison result written to [...]/<suite>-dynamic-results.csv.
```

Check that this produces a CSV comparing pass/fail results across the three
projects for the chosen suite.

> [!WARNING]
> The `v1model+` test suite has over 1900 tests. Running it on HOL4P4 will take
> a long time to finish, even up to 12+ hours.

To run a specific test suite with a single project, pass the project name to
the second argument of the `rq3`. For example, execute the following command to
run the `ebpf` test suite with HOL4P4.

```bash
$ scripts/rq3 -i p4include -e testdata/excludes/ -t testdata -l logs \
   --spectec p4-spectec --petr4 petr4 --hol4p4 HOL4P4 \
   dynamic HOL4P4 ebpf
```

#### Reproducing Table 6

Run `scripts/rq3-table6` to render Table 6 (Comparison of Petr4, HOL4P4, and
P4-SpecTec) from the paper, reading directly from `logs/rq3-pre/`:

```bash
$ scripts/rq3-table6 -l logs
```

Pass `--live` to instead read from `logs/rq3/`, i.e. the output of an actual
`scripts/rq3` run:

```bash
$ scripts/rq3-table6 -l logs --live
```
