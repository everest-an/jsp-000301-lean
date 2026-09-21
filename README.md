# JSP-000301 · Golomb's counterexample, formalised in Lean 4

Lean 4 formalisation of the resolution of **JSP-000301** from
[The Justin Sun Prize problem bank](https://github.com/TheJustinSunPrize/awards/blob/main/problems/catalog-0301-0400.md#JSP-000301):

> **Problem.** If two consecutive positive integers are powerful, must at least
> one be a perfect square?

The problem bank records the answer as **No**, with Solomon W. Golomb's
counterexample (*Powerful numbers*, Amer. Math. Monthly **77**(8) (1970), 848–852).

This repository contains a complete Lean 4 proof of that counterexample.

## The counterexample

```
12167 = 23^3
12168 = 2^3 * 3^2 * 13^2 = 12167 + 1
```

Both are consecutive positive integers, both are powerful (every prime factor
occurs to at least the second power), and neither is a perfect square
(`110^2 = 12100 < 12167, 12168 < 12321 = 111^2`). One such pair refutes the
universal statement.

## Formalised statement

`Jsp000301.lean` defines

```lean
def Powerful (n : ℕ) : Prop := ∀ p : ℕ, p.Prime → p ∣ n → p ^ 2 ∣ n
def IsSquare (n : ℕ) : Prop := ∃ k : ℕ, k * k = n
```

and proves

```lean
theorem jsp_000301 :
    ¬ (∀ n : ℕ, 0 < n → Powerful n → Powerful (n + 1) →
        IsSquare n ∨ IsSquare (n + 1))
```

together with the four supporting facts `powerful_12167`, `powerful_12168`,
`not_square_12167`, `not_square_12168`.

## Trust level

`#print axioms JSP000301.jsp_000301` reports

```
[propext, Classical.choice, Quot.sound]
```

No `sorry`, no custom axioms, no `native_decide`. See
[`verification/report.md`](verification/report.md) for the full verification
report, including the review level actually reached and the limits of the
conclusion.

## Reproduce

```bash
lake exe cache get
lake build +Jsp000301
lake env lean Jsp000301.lean
```

Toolchain: `leanprover/lean4:v4.35.0-rc2`.
Mathlib: pinned in `lake-manifest.json`.

To check the target's axioms:

```bash
cat > AuditTarget.lean <<'EOF'
import Jsp000301
set_option pp.all true
#check @JSP000301.jsp_000301
#print axioms JSP000301.jsp_000301
EOF
lake env lean AuditTarget.lean
```

## Scope

This repository covers JSP-000301 only — the yes/no question quoted above. It
does **not** address the separate counting question of
[Erdős problem #365](https://www.erdosproblems.com/365).

Nothing here decides merge, award or payment. Lean verification establishes
proof integrity only.
