[README.md](https://github.com/user-attachments/files/32175929/README.md)
# Genus-two masked residue-tree validation companion

This archive accompanies `genus2_masked_residue_tree.tex`. The English
manuscript is self-contained and compiles with two runs of `pdflatex`.
It preserves the six main sections, recovery weights (2,2,3,3), CFS as
reference [4], and the Chen comparison. The new construction is developed
in Section 5 and instantiated in Section 6.

## Complete reversible CRT supplement (2026-09-14)

This cumulative update adds the full streaming reconstruction trace R,
its inverse, an arbitrary-target clean XOR wrapper, and R^-1 Phi R cleanup.
It reuses the previous tree-scheduling supplement. See `CRT_CHANGELOG.md`
for Chinese file/function changes, a worked negative-integer example,
commands, validation coverage, and manuscript alignment notes.

```sh
python3 reversible_crt.py --trace crt_reconstruction_trace.json
python3 validate_reversible_crt.py
python3 audit_streaming_ledger.py
```

`reversible_crt.py` streams quotient digits into A and uses its rounded high
bits for correction, without separate digit/quotient registers.
`crt_gate_backend.py` compiles small complete CRT examples into X/CNOT/Toffoli
networks using the existing primitives. It reports all reference-backend
workspace; it is not a new certificate of cryptographic-size width.
The full validator covers signed boundaries, actual four-leaf polynomials,
arbitrary XOR targets, phase cleanup, dirty-bit restoration and intentional
faults. The existing ledger entry point now also runs 21 complete CRT smoke
cases, while retaining the published report schema and resource numbers.
Root gates use small truth-table XOR circuits in the gate backend; character
phases are specified diagonal operations, with Jacobi gate validation kept
in the existing separate audit. Do not use Python `-O` for these checks.

## Full-tree schedule supplement (2026-09-14)

This revised code bundle adds an explicit complete measurement schedule,
including all ghost-phase correction. See `TREE_SCHEDULE_NOTES.md` for
Chinese integration instructions, proof, exact scope, and manuscript edits;
`tree_schedule_appendix.tex` provides an English proof fragment. The original
manuscript source is not included here and has not been modified.

```sh
python3 residue_tree_schedule.py --height 5 --trace tree_schedule_h5.json
python3 validate_residue_tree_schedule.py
python3 audit_streaming_ledger.py
```

The schedule has `(4h-2)L+3` macros and at most `h+2` vector pebbles.
The ledger now checks an actual full-tree certificate before applying its
existing conservative bounds, so the numerical tables below are unchanged.
The new validator checks symbolic transcript cleanup, 1,324 conditional
state evolutions with arbitrary targets and entangled references, fresh
forward XOR cleanup, and 32 literal local arithmetic cases. The local
bridge emits the existing field-operation traces for phase correction and
root consumption. This is not elementary-gate synthesis of the whole tree.
Use ordinary Python, without `-O`, because audit assertions must remain on.

## What is established

The compact generic addition node has output degrees (32,31,48,47,15)
and weighted-height growth factor 20. Its clean recursive implementation
uses eight local field words and 15,287 multiply-accumulate plus 20,218
square-accumulate operations. Public zero-sum masks make this fixed
polynomial tree a bounded-error phase sampler on the exact uniform
domain Z_r^2. This is not exact complete group addition on arbitrary inputs.

For the published Gaudry–Schost instance with q=2^127−1, subgroup bit
length 250, and 500 simultaneous input bits:

| Window | CRT peak | Character peak | Preparation/Fourier peak | Total | Chen allocation |
|---|---:|---:|---:|---:|---:|
| 16 | 1977 | 1326 | 755 | 1977 | 3063 |
| 32 | 1667 | 1320 | 755 | 1667 | 3063 |

The reductions are 35.4554% and 45.5762%. These are conservative logical
allocations for the specified circuits, not lower bounds on alternatives.
The 16-bit-window CRT-phase Toffoli upper bound alone is
2,908,862,055,047,641,311,408 (approximately 2^71.30094). Classical table
entry bounds are 2,097,152 and 68,719,476,736. There is no claimed gate-count
or practical-runtime improvement over Chen.

The arithmetic useful-sample failure bound for 32 leaves is below
1.795e−34. With 42 preparation trials per input and Fourier and rotation
synthesis errors each at most 2^−40, the per-run verified useful-sample
probability is greater than 1−2^−38. The concrete parameter order is cited
from the published construction; the supplied checks do not independently
prove the full Jacobian cardinality.

## Reproduce

Extract all files into one directory and run commands there. Python 3.10+
is required. `validate_sampling.py` and `validate_fourier_space.py` use
NumPy; the algebra, integer ledger, and elementary-gate checks use the
standard library. The scripts share local imports, so retain them together.

The main new checks are:

```sh
python3 independent_compact_review.py
python3 audit_compact_weighted.py
python3 audit_streaming_ledger.py
python3 validate_gaudry_schost.py
python3 validate_modular_primitives.py
python3 validate_jacobi_gates.py
python3 validate_fourier_space.py
python3 validate_masked_tree.py
```

The final masked-tree command exhaustively enumerates small configurations
and can take longer than the other commands. Scripts print reports; some
also write their named JSON report beside the source. To retain a printed
report, redirect stdout to a new `.json` file. The included JSON files
record successful executions; `streaming_ledger_report.json` uses the
correct separate-window count 2 ceil(m_r/w), explicit 6n+12 Jacobi reserve,
and the maximum over all relevant stages.

## Coverage and limits

- `independent_compact_review.py`: independent exact sparse-polynomial
  interpolation, quotient, reduction, weighted-degree and coefficient-norm
  certificates. This is an identity check, not finite-field sampling.
- `audit_compact_weighted.py`: 4,012 small-field and 110 127-bit-field
  generic additions; 150 integer/modular commutation checks; 25 executions
  and reversals of the literal 35,505-operation schedule with arbitrary
  target accumulators. `audit_recursive_expression.py` supplies its
  expression scheduler; running that helper alone audits the earlier map.
- `audit_streaming_ledger.py`: 32,760 quotient-digit cases and the finite
  prime-product/prime-count certificates using Dusart's bounds. The millions
  of auxiliary primes are not explicitly generated.
- `validate_masked_tree.py`: 74,145 two-leaf phase checks and 1,443,128
  four-leaf phase evaluations for each of the old and compact maps over
  q=3,5, including 111,152 good-tree checks per map. The conservative
  exceptional-probability bound is vacuous on these tiny fields; this is
  stated explicitly in `MASKED_TREE_VALIDATION.md`.
- `validate_modular_primitives.py`: 7,855 actual X/CNOT/Toffoli network
  cases, including borrowed-bit restoration, clean modular addition,
  multiplication, squares, and doubling.
- `validate_jacobi_gates.py`: 618 actual small-prime-field network cases,
  checking completed-character phases and all cleanup. The certificate is
  6n+8 actual qubits, with an advertised 6n+12 reserve, including the input.
- `validate_fourier_space.py`: 580,630 modular-shift basis cases, 2,013
  phase-estimation distributions, 38 adaptive readouts, and 60 entangled
  input checks. These are arithmetic/numerical protocol checks, not a
  compiled quantum Fourier circuit at cryptographic size.
- `validate_gaudry_schost.py`: curve-model conversion, generator membership,
  scalar-order tests, and addition/isomorphism identities. Lucas–Lehmer
  proves q prime; fixed-base Miller–Rabin on r is only a sanity check.

The complete cryptographic-size residue sampler has not been synthesized
as an elementary-gate netlist or fault-tolerant layout. The paper combines
explicit component constructions with mathematical composition proofs and
conservative operation/allocation ledgers. Measurements, reset, classical
feedback, tables and mask generation are allowed and accounted for in the
stated resource model.

## Retained baseline checks

The prior validation scripts and reports remain for the original full-output
and terminal schedules, complete Cantor arithmetic, fixed binary traces,
canonical compression, and small-instance sampling. Their details are in
`LEGACY_README.md`, `REPORT.md`, and `TERMINAL_VALIDATION.md`. Statements
there about an uninstantiated low-space route describe the previous version;
the current approximate masked construction and its scope are described
above and in the revised manuscript. The earlier exact arbitrary-input
residue interface remains conditional.

```sh
python3 audit.py
python3 cantor_complete.py
python3 validate_binary_backend.py
python3 audit_binary_jacobi.py
python3 audit_generic_schedule.py
python3 audit_optimized_certificate.py
python3 audit_phase_terminal.py
python3 sparse_certificate.py
python3 validate_sampling.py
```

`MANIFEST.json` records the accompanying manuscript SHA-256 and all files
in this validation archive. MATLAB listings retained in the manuscript
were not executed in this environment.
