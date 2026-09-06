# ACL2-rectifiers-check

Independent, machine-checked (ACL2(r)) verification of the master
equations in:

> P. O. Cayuela, "Deduccion y Verificacion de las Ecuaciones de
> Comportamiento de Rectificadores No Controlados, Semicontrolados y
> Controlados en Configuraciones Multifasicas," Congreso Parana, 2026.

and a first, concrete instantiation of the roadmap sketched (but never
run) in its companion paper:

> P. O. Cayuela, "Towards Mechanized Verification of q-Pulse Rectifier
> Voltage Equations in ACL2(r)," submitted to the ACL2 Workshop 2026.

That roadmap paper states explicitly: *"Nothing in Section 5 has yet
been submitted to the theorem prover."* This repository is the first
submission — the exact-value algebraic layer of that roadmap, run for
real in ACL2(r).

## What is proved

| File | Paper source | Result |
|---|---|---|
| `00-sampling-function.lisp` | eq. (1), Sa(x)=sin(x)/x | Exact value of Sa at pi/6, pi/3, 2pi/3, pi/2, pi — the arguments the master equations need for every topology worked in the paper |
| `01-topology-instantiation.lisp` | eqs. (7)-(13), Table I/IV | **`eq12-VCC-bridge-3ph-coefficient`** confirms the 3-phase bridge VCC coefficient; **`bridge-vs-midpoint-Sa-differ`** formally confirms the coefficient slip caught numerically in Sec. V/4 of the two papers (Sa(pi/3) and Sa(2pi/3) are provably different values); **`bifasico-T-coincides`** formalizes that the single-phase full-wave row (bridge family, q=1) and a two-phase midpoint rectifier (midpoint family, q=2) are literally the same instantiation of the master equations; **`q1-table-VRMS-row-inconsistent-with-master-eq`** is a new finding from this mechanization — see below |
| `02-semiconverter-cross-checks.lisp` | eqs. (14)-(16), Sec. 5.3 of the roadmap | The three semi-converter cross-checks sketched as unproved `i-close` conjectures in the roadmap — continuity at alpha=pi/3, and the two boundary evaluations at alpha=0 and alpha=pi — proved here as **exact** equalities |
| `top.lisp` | — | Includes all of the above; certifying it certifies the whole chain from scratch |

## A new finding

`q1-table-VRMS-row-inconsistent-with-master-eq` shows that the
"Onda completa 1~" / "Single-phase full-wave" row of Table IV/Table 1,
which lists VRMS/VmRMS = sqrt(1+cos(2*alpha)), is inconsistent with the
paper's own master equation (8): for that row's family (T=pi), Sa(T) =
Sa(pi) = 0 exactly, so eq. (8) forces VRMS/VmRMS to be **constant**
(=1) for every alpha — which is in fact the standard, if slightly
non-obvious, power-electronics result that the RMS output of a
single-phase full converter under continuous conduction does not
depend on the firing angle. The table's printed formula is not
constant (it equals sqrt(1/2) at alpha=pi/3, for instance), so the
table entry should be corrected in a future revision of the paper.

## Honesty boundary — what this does *not* prove

- **The integral-based master theorems** proposed in Section 5.2 of
  the roadmap (`vcc-master-theorem`, `vrms-master-theorem`, stated
  against a nonstandard Riemann-integral definition of VCC/VRMS via
  `defintegral`) are **not attempted here**. The community books do
  provide the needed machinery in principle
  (`books/nonstd/integrals/ftc-2.lisp`, a generic Fundamental Theorem
  of Calculus via `encapsulate`), but functionally instantiating it
  for the integrand cos(theta+alpha) requires a derivative-of-sine/
  cosine theorem this installation's `nonstd/nsa` books do not already
  provide — a substantially larger undertaking than this repository's
  scope. What is proved here instead verifies the same conclusions
  (the closed-form coefficients, the regime cross-checks) starting
  from the closed forms already derived by hand in the paper, not from
  the defining pulse integral itself.
- The q=6 (hexafasico) and twelve-pulse rows of Table IV/Table 1 are
  **not checked**. Unlike the rows checked here, their Vm does not
  come from a direct substitution into the two-family model of
  Table I/Section II; the paper justifies them by an additional
  circuit-topology argument (transformer winding configuration) that
  falls outside the master-equation instantiation this repository
  mechanizes.
- Single-phase half-wave (q=1, midpoint family) is out of scope for
  the same reason the paper itself excludes it from Table IV: the
  "contiguous pulses" assumption behind the whole model does not hold
  for that topology.

None of this weakens what *is* proved here — it marks the line between
what this mechanization checked and what remains for a follow-up
development.

## Requirements

- ACL2(r) — the nonstandard-analysis extension of
  [ACL2](https://www.cs.utexas.edu/users/moore/acl2/) — with the
  standard community books available (`arithmetic-5/top` and
  `nonstd/nsa/trig` are used, both `:dir :system`).

## Certifying

```sh
export ACL2=/path/to/saved_acl2r
cert.pl --acl2 "$ACL2" top
```

This certifies `top.lisp` and, transitively, all three books in
dependency order. A clean run should produce no errors.

## Citation

If you use this in academic work, please cite the two papers above.
Suggested citation for this repository:

```
P. O. Cayuela, "ACL2-rectifiers-check: mechanized verification of the
q-pulse rectifier master equations", 2026.
https://github.com/pcayuela/ACL2-rectifiers-check
```

## License

MIT — see [LICENSE](LICENSE).

## Contact

Pablo Oscar Cayuela — Centro Universitario de Desarrollo en
Automación y Robótica, Depto. Ing. Electrónica, Universidad
Tecnológica Nacional, Facultad Regional Córdoba, Argentina.
pcayuela@frc.utn.edu.ar
