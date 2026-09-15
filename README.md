[README.md](https://github.com/user-attachments/files/32250787/README.md)
# Genus-two quantum discrete logarithms: submission revision (37 pages)

The complete manuscript contains **27 main-text pages, 2 reference pages,
and 8 appendix pages**, totaling **37 pages**. The title and abstract count
within the main text. The build enforces both 27+2 pages and a 40-page total
limit. The seven sections and 23 main-text subsections retain their order.

## Files

- `manuscript.tex`: standalone English LaTeX, bibliography, and Appendices A-E.
- `manuscript.pdf`: complete 37-page paper, including all retained appendices.
- `article_with_references.pdf`: 29-page extract through the bibliography.
- `llncs.cls`: unchanged user-supplied LLNCS v2.13 class.
- `build.py`: three-pass compilation, reference/overflow/page checks, and PDF extraction.
- `page_count.json`: measured pagination and template information.
- `revision_notes.md`: Chinese submission-revision report, validation, and remaining scope.
- `checks/`: 20 Python checkers and their certificates/results.
- `matlab/`: three MATLAB files exported unchanged from the previous appendix listings.
- `artifact_manifest.json`: SHA-256 hashes and sizes of the packaged files.

The source does not require an external bibliography or appendix input file.
The code and certificate files support reproducibility but are not imported by
LaTeX. The shortened PDF's appendix links require the complete PDF.

## Submission revision

This edition develops the 36-page condensed manuscript into a 37-page
submission-oriented author draft. The seven-section framework, 27-page
main text, two-page bibliography, resource values, and executable checks
are retained. The original extended draft had 86 pages.

The abstract and Introduction distinguish the masking theorem, phase-sampling
argument, and finite resource allocation. The measurement/reset model and
the cost of reversible table selection are stated earlier. Contribution and
Organization remain at the end of Introduction. The comparison and Conclusion
have been edited to give each scope qualification at its natural location.

The main theorem now has an explicit proof roadmap. New Appendix D.4 derives
O(L/q) arithmetic failure, proves that the coordinatewise height certificate
is bounded by the weighted certificate, supplies a polynomial auxiliary-prime
schedule, and computes all four asymptotic stage widths. This supplies the
steps behind the existing 10n+o(n) statement without changing any resource
number or asserting a new theorem.

A malformed exceptional-set summation index in Appendix B is corrected by
explicitly defining the bad-input set. Register rank is denoted by rho(e),
separately from subgroup order r. The support and coefficient norm are defined
after collection of equal monomials. Stale descriptions placing code in the
appendices are corrected; programs and recorded results are in this archive.
Table captions are consistently above the tables, and the notation-index
heading is kept with its table.

Appendices A-C retain complete arithmetic, integer identities, sampling
reductions, reconstruction, and measured-tree cleanup. Appendix D retains
the modular/Jacobi decomposition, shared-expression and scalar-ghost
certificates, height and parameter proofs, and the new asymptotic derivation.
Appendix E contains the Chen inventory, comparison scope, and notation index.
The PDF contains the essential supporting arguments; long program listings
are provided as separate executable files.

## Compilation

With pdfLaTeX, Python 3, and PyMuPDF installed, run from this directory:

```sh
python3 build.py
```

The builder compiles in a temporary directory and replaces PDFs only after
validation. It rejects unresolved references, duplicate labels or link targets,
and overfull boxes. It requires 27 main-text pages, 29 through references,
and at most 40 pages overall. All 24 bibliography entries are cited; CFS
remains reference [4]. The class, 10-point text, Letter paper, and one-inch
margins are unchanged. Different TeX installations can change pagination.
Authors and affiliations are retained; this package does not certify a
specific conference cycle's anonymity or formatting requirements.

## Reproducing the checks

The checks use Python 3; `check_sampling.py` additionally requires NumPy.
Keep all files in `checks/` together. To run all 20 checkers in filename order:

```sh
python3 - <<'CHECKS'
from pathlib import Path
import subprocess
import sys
for script in sorted(Path('checks').glob('*.py')):
    subprocess.run([sys.executable, str(script)], check=True)
CHECKS
```

The normalized-height producer precedes the resource ledger that consumes
its result. Individual examples:

```sh
python3 checks/check_cached_symbolic.py
python3 checks/check_cached_gate_integration.py
python3 checks/check_height_certificate_independent.py
python3 checks/check_resource_ledger.py
```

Some reference checkers retain comparisons with older arithmetic schedules
or sampling examples that no longer need full exposition in the compressed
PDF. Their result files are preserved as reproducibility material, not new
research claims. This pass independently reviewed the added asymptotic derivation and the
revised proof interfaces, and checked cross-references and layout. It did not
rerun all historical experiments; their preserved results are not new tests.

## Scope of the resource claims

The unchanged 16-bit and 32-bit window allocations are 1,923 and 1,618 logical
qubits, versus a declared Chen-derived matched allocation of 3,063.
The 16-bit-window capped-run Toffoli upper bound is
1,128,039,642,233,455,592 < 2^60, with rotation synthesis charged separately.
These are analytical allocations and bounds, not measured performance or
optimality claims. The full cryptographic-size circuit has not been compiled.

The retained gate-integration results cover 24 targeted actual gate-network
cases with shared scratch; other checks cover exact algebra, local traces,
parameter certificates, masking, sampling, and pebbling. Finite tests supplement
the proofs and do not establish untested universal claims. MATLAB was checked
through a Python transcription, not execution in MATLAB. The short-rectangle
Legendre sampling guarantee and practical gate-count improvement remain open
limitations, as stated in the paper.
