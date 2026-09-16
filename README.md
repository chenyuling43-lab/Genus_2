[README.md](https://github.com/user-attachments/files/32269934/README.md)
# Genus-two arithmetic and verification code

This code-only supplement contains the weighted-projective genus-two addition
example and independent verification programs. No LaTeX installation is needed.

## Quick start

Extract the ZIP completely before running anything. Keep the directory
structure intact. Choose the MATLAB example or the independent Python suite;
neither entry point requires the other language.

### MATLAB: open and run

Requires **MATLAB with Symbolic Math Toolbox**.

Open `RUN_MATLAB.m` in MATLAB and click **Run**. Alternatively, set the current
folder to `Genus2_Code` and enter:

```matlab
RUN_MATLAB
```

The runner adds the implementation directory temporarily, executes the
supplied example and its assertions, and restores the MATLAB path afterward.
No coordinate editing or manual path setup is required. Expected results:

```text
Projective [U1 U0 V1 V0 Z]: [81 53 65 52 68]
Affine     [u1 u0 v1 v0]:   [56 94 30 24]
```

An exception means the run did not pass. The runner prints PASS only after
the example returns successfully. This entry executes the original example,
not a complete MATLAB regression suite.

### Python: all 20 checks

Requires **Python 3.10 or newer**. NumPy is needed only for `check_sampling.py`.
If NumPy is not already installed, install the declared dependency once:

```sh
python -m pip install -r requirements.txt
```

Then run from this directory:

```sh
python RUN_PYTHON.py
```

On Windows, `RUN_PYTHON.bat` is a double-click launcher (Python must already
be installed). On Linux/macOS, use `python3 RUN_PYTHON.py` or
`sh RUN_PYTHON.sh`. An absolute path to the Python runner works from another
current directory as well, including paths containing spaces.

To run the 19 standard-library checks without NumPy:

```sh
python RUN_PYTHON.py --standard-library-only
```

This explicitly reports one skipped FFT check; it is not a full-suite pass.
Do not use `python -O`, `python -OO` or `PYTHONOPTIMIZE`, because assertions
are part of the verification. The runner refuses optimized execution.

The runner executes checks in dependency order, prints progress, and stops
on a failure. It makes no network requests and installs no packages. Each
run writes a separate folder under `results/python/`, including logs,
computed JSON results and `summary.json`. Expected successful full output:

```text
PASSED: 20/20 checks passed; 0 skipped.
```

Use `--output-dir PATH` to select another writable results parent directory.
The supplied source and reference data are not overwritten during a run.

## Contents

| Path | Purpose |
| --- | --- |
| `RUN_MATLAB.m` | Single entry point for the supplied MATLAB example |
| `RUN_PYTHON.py`, `.bat`, `.sh` | Independent verification entry points |
| `Reference_Implementation/MATLAB/` | Original three MATLAB source files |
| `Verification/Python/` | Original 20 Python verification scripts |
| `KAT/subgroup_prime_certificate.json` | Input certificate required by the subgroup checks |
| `KAT/Expected_Results/` | Reference JSON results supplied with the source archive |
| `requirements.txt` | Python dependency for the complete suite |
| `artifact_manifest.json` | File sizes and SHA-256 hashes; excludes itself |

The layout separates reference code, verification programs and known-answer
data. No architecture-specific or full optimized quantum implementation is
included. Reference JSON files are comparison data, not evidence of a new run;
fresh results are created by `RUN_PYTHON.py`. Tiny FFT roundoff differences
between environments are handled by the sampling check's stated tolerance.
Run the root Python entry point rather than individual relocated scripts:
it places the certificate alongside the scripts in a temporary working copy
and generates dependent results before their consumers execute.

## MATLAB interface and scope

For interactive use, add `Reference_Implementation/MATLAB` to the MATLAB path:

```matlab
p = sym('101');
curve = sym([3 5 7 11]);
D = sym([57 11 53 60 2]);
L = sym([49 42 94 99 3]);
[R, info] = hec2_projective_add(D, L, p, curve, 'a4');
affine = hec2_projective_recover(R, p);
```

The curve model is `y^2 = x^5 + c3*x^3 + c2*x^2 + c1*x + c0` over an odd
prime field, with `curve=[c0 c1 c2 c3]`. Coordinates are
`[U1 U0 V1 V0 Z]`, decoded as `[U1/Z^2 U0/Z^2 V1/Z^3 V0/Z^3]` in the field.

This source version implements **generic addition only**. Inputs must be valid
degree-two divisors on the same smooth curve; the routine does not validate
curve membership. It rejects zero scales and its H=0 and B3=0 exceptional
branches. It does not provide a complete Cantor backend, doubling or identity
handling. The a0 route additionally requires a nonzero product of input U0
coordinates. The main formulas use no modular inverse; affine recovery does.

Use `sym('integer')` or exact symbolic expressions for large integers; do not
first compute a large value in double precision and then convert it to sym.
The classical functions are not a constant-time cryptographic library.

## Verification coverage and status

The Python programs check exact polynomial identities, finite-field arithmetic,
sampling examples, parameter certificates, height/resource formulas, local
reversible arithmetic circuits and scheduling. They do not compile the full
cryptographic-size quantum sampler. Some scripts deliberately retain labeled
older schedules as comparison data.

The MATLAB formula checker is a Python transcription, not execution of `.m`
files. The MATLAB entry point requires validation in MATLAB with Symbolic Math
Toolbox; no native MATLAB execution is claimed for this package. Static MATLAB
syntax checks and Python checks cannot replace that native run.
