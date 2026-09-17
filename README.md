[README.md](https://github.com/user-attachments/files/32320218/README.md)
# Reproducibility code for the genus-two quantum DLP manuscript

This package contains 20 checks of the paper's algebra, finite examples,
local reversible circuits, and resource formulas. Each check produces a JSON
report. The runner compares every result with the retained reference output
and exits with a nonzero status if a check fails, times out, or differs.

## Start here

Use Python 3.11 or later. The quick suite uses only the standard library:

```sh
python3 run_checks.py --suite quick
```

For all 20 checks, install the single numerical dependency and run:

```sh
python3 -m pip install -r requirements.txt
python3 run_checks.py --suite full
```

The recorded full run and its environment are documented in
`verification/verification_report.json`. The MATLAB algebra result includes
SHA-256 identities for the reviewed MATLAB source and Python transcription.
A changed MATLAB source is rejected until its transcription has been reviewed. The quick suite is a useful first
check, but the full suite is needed to reproduce all retained outputs.
Execution is local; the checkers do not contact external services.

The runner prints one status per checker and places fresh JSON files,
individual logs, and `verification_report.json` in a new directory under
`results/`. It does not reuse a previous run or modify `expected/`.

## Select a result

List the registered checks:

```sh
python3 run_checks.py --list
```

Recompute the resource table and its height-certificate prerequisite:

```sh
python3 run_checks.py --check check_resource_ledger
```

Check the cached circuit integration:

```sh
python3 run_checks.py --check check_cached_gate_integration
```

`--check` may be repeated. `--output-dir PATH` selects a different parent
for run directories; `--timeout SECONDS` changes the per-check time limit
(default: 900 seconds). Paths to the package are resolved from the scripts,
so the runner also works from another working directory.

Individual checkers can still be run directly:

```sh
python3 checks/check_normalized_height.py
python3 checks/check_resource_ledger.py
python3 checks/check_cached_symbolic.py --output symbolic_result.json
```

Direct execution runs assertions and writes a result, without comparing it
with `expected/`. The resource ledger requires the height report first;
the runner resolves this dependency automatically. Do not use Python's `-O`
option or `PYTHONOPTIMIZE`: the programs reject execution with assertions
disabled.

## Package layout

| Path | Purpose |
| --- | --- |
| `run_checks.py` | Check registry, dependency order, execution and comparison |
| `checks/` | 20 executable checkers and three shared support modules |
| `certificates/` | Input certificate for the subgroup primality check |
| `expected/` | Frozen reference JSON outputs, kept separate from new results |
| `verification/` | Python full-suite report and logs; author-supplied MATLAB fixed-example diary and scope note |
| `VERIFICATION_MAP.md` | Paper claims, corresponding checks, and coverage limits |
| `matlab/` | Projective-addition reference functions and demonstration |
| `requirements.txt` | Dependency for the NumPy Fourier-sampling check |
| `matlab/SOURCE_CORRESPONDENCE.md` | Final MATLAB source identity and Python formula correspondence |
| `artifact_manifest.json` | SHA-256 hashes and sizes of the packaged files |

`checks/polynomial_model.py` defines the shared sparse-polynomial model.
`checks/cantor_reference.py` provides finite-field reference arithmetic.
`checks/artifact_io.py` handles certificates, command-line options and atomic
result writes. Importing a checker does not run its experiments or write files.
The independent proof and height checkers retain their separate arithmetic
implementations.

## Reading the results

The retained JSON keys, mathematical fixtures, deterministic seeds and
reported numerical values are preserved. Integers, booleans, strings and
container structure are compared exactly. JSON floating-point values allow
relative and absolute tolerances of `1e-12` for platform-dependent roundoff;
large integer resource counts never pass through floating-point conversion.
`exact_json_match` in the run report also records whether each parsed output
is exactly equal to its reference. Decimal values stored as strings are
compared exactly.

`expected/` is a regression reference, not a source of computed answers.
The checkers calculate their outputs from their formulas, finite inputs and
certificates. They do not load these expected files. Matching an old output
supports preservation during refactoring; the assertions and independent
comparisons inside each checker provide the scientific checks. Consult
`VERIFICATION_MAP.md` for what each one establishes.

Historical comparison fields in the JSON are retained for traceability. They
are distinguished from the current cached-evaluator results and do not alter
the paper's headline resource allocation. Script filenames remain stable to
match citations in the manuscript.

## Scope

Finite checks supplement the paper's proofs. The package does not compile
or execute a cryptographic-size quantum sampler. The resource ledger evaluates
analytical bounds, with rotation synthesis accounted for separately in the
paper. Gate integration covers the stated local test cases. The Python suite
checks the MATLAB algebraic transcription. Separately, an author-supplied
MATLAB R2026a Update 5 diary records successful fixed-example execution of
both recovery routes; see `verification/MATLAB_VALIDATION.md`. This does not
extend the MATLAB execution claim to the seeded Python tests or a full sampler.
The per-check limitations are given in the verification map.
