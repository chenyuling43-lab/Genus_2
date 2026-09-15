[README.md](https://github.com/user-attachments/files/32175929/README.md)
# Genus-two masked residue-tree validation

This package contains arithmetic implementations, residue-tree schedules,
reversible CRT reconstruction, and validation scripts for genus-two
phase sampling. The associated manuscript is
`genus2_masked_residue_tree.tex`; its source is not included.
`tree_schedule_appendix.tex` contains a supplementary proof fragment.

## Reversible CRT reconstruction

`reversible_crt.py` implements streaming reconstruction R, its inverse,
a clean XOR interface for arbitrary targets, and R^-1 Phi R phase cleanup.
It accumulates quotient digits in A and uses the rounded high bits for
correction, without separate digit or quotient registers.
`CRT_NOTES.md` describes the registers, a signed-integer example, and
validation coverage.

```sh
python3 reversible_crt.py --trace crt_reconstruction_trace.json
python3 validate_reversible_crt.py
python3 crt_gate_backend.py --netlist crt_small_gate_network.json
python3 audit_streaming_ledger.py
```

`crt_gate_backend.py` compiles small CRT examples into X/CNOT/Toffoli
networks and reports their workspace. The validator covers signed
boundaries, four-leaf polynomials, arbitrary XOR targets, phase cleanup,
borrowed-bit restoration, and intentional faults. The ledger script
also runs 21 complete CRT cases.

The small gate backend uses truth-table XOR circuits for root evaluation.
Character phases are specified diagonal operations; Jacobi gates are
checked separately. These component checks do not constitute synthesis
of the cryptographic-size sampler.

## Residue-tree scheduling

`residue_tree_schedule.py` generates the measurement schedule and
corrections for all recorded phases. `TREE_SCHEDULE_NOTES.md` gives the
macro definitions, correctness argument, and resource bounds.

```sh
python3 residue_tree_schedule.py --height 5 --trace tree_schedule_h5.json
python3 validate_residue_tree_schedule.py
```

The schedule has `(4h-2)L+3` macros and uses at most `h+2` vector pebbles.
The resource ledger checks the full-tree schedule certificate before
applying its conservative bounds. The included validation report records
1,324 conditional state evolutions with arbitrary targets and entangled
references, fresh forward XOR cleanup, and 32 local arithmetic cases.
The local macros provide field-operation traces for phase correction
and root consumption.

Run validation with assertions enabled; do not use Python `-O`.

## Arithmetic and resource bounds

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
from the published construction; these checks do not independently
prove the full Jacobian cardinality.

## Reproduce

Extract all files into one directory and run commands there. Python 3.10+
is required. Install NumPy for sampling, Fourier, masked-tree, tree-schedule,
and complete CRT validation. The algebra, integer ledger, and standalone
modular/Jacobi gate checks use the standard library. The scripts share local imports, so retain them together.

Run the arithmetic and resource checks with:

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
report, redirect stdout to a separate `.json` file. The included JSON files
record successful executions; `streaming_ledger_report.json` uses the
separate-window count 2 ceil(m_r/w), explicit 6n+12 Jacobi reserve,
and the maximum over all relevant stages.

## Coverage and limits

- `independent_compact_review.py`: independent exact sparse-polynomial
  interpolation, quotient, reduction, weighted-degree and coefficient-norm
  certificates. This is an identity check, not finite-field sampling.
- `audit_compact_weighted.py`: 4,012 small-field and 110 127-bit-field
  generic additions; 150 integer/modular commutation checks; 25 executions
  and reversals of the literal 35,505-operation schedule with arbitrary
  target accumulators. `audit_recursive_expression.py` supplies its
  expression scheduler; running that helper alone audits the degree-165 map.
- `audit_streaming_ledger.py`: 32,760 quotient-digit cases and the finite
  prime-product/prime-count certificates using Dusart's bounds. The millions
  of auxiliary primes are not explicitly generated.
- `validate_masked_tree.py`: 74,145 two-leaf phase checks and 1,443,128
  four-leaf phase evaluations for each of the degree-165 and compact maps over
  q=3,5, including 111,152 good-tree checks per map. The conservative
  exceptional-probability bound is vacuous on these tiny fields; this is
  stated explicitly in `MASKED_TREE_VALIDATION.md`.
- `validate_modular_primitives.py`: 7,855 actual X/CNOT/Toffoli network
  cases, including borrowed-bit restoration, clean modular addition,
  multiplication, squares, and doubling.
- `validate_jacobi_gates.py`: 618 actual small-prime-field network cases,
  checking completed-character phases and all cleanup. The certificate is
  6n+8 actual qubits, with a conservative 6n+12 reserve, including the input.
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

## Baseline arithmetic and phase checks

The full-output and terminal schedules, complete Cantor arithmetic,
fixed binary traces, canonical compression, and small-instance sampling
are documented in `BASELINE_README.md`, `REPORT.md`, and
`TERMINAL_VALIDATION.md`. The exact arbitrary-input residue construction
requires a complete CRT-compatible arithmetic program; the masked sampler
uses the approximate construction described above.

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

`MANIFEST.json` records file hashes and the associated manuscript hash.
The manuscript source is not included in this archive. MATLAB listings
are outside the reported computational validation.
