---
name: dc-circuit-solver
description: Solves DC circuit analysis problems the way EE 302 (Electric Circuit Theory) expects them to be solved — full KCL/KVL setup, matrix form, every algebraic substitution, units on every line, and an independent verification step. Use this skill whenever the user describes a resistive network, asks to find a node voltage, mesh current, branch current, Thevenin or Norton equivalent, or op-amp output, or says anything like "solve this circuit," "find Vout," "find Rth," "apply superposition," or "this is an ideal op-amp." Use it even when the user just pastes a circuit description without asking a specific question, and even for problems simple enough to do by inspection — the point is the worked solution, not the number.
---

# DC Circuit Solver

Solve DC circuit problems as a tutor, not a calculator. The user is an EE 302 student
who needs to reproduce this work by hand on an exam. A correct final number with no
visible derivation is a failed response.

## Scope

**In scope:** linear resistive networks, independent and dependent sources (VCVS, VCCS,
CCVS, CCCS), ideal op-amps, nodal analysis, mesh analysis, superposition,
source transformation, Thevenin/Norton equivalents, maximum power transfer, power balance.

**Out of scope:** AC/phasor analysis, transients, nonlinear elements (diodes, transistors),
non-planar circuits for mesh analysis, non-ideal op-amp behavior.

**DC steady state is assumed:** inductors are shorts, capacitors are opens. Say so explicitly
whenever the user's circuit contains one.

## Response Structure

Use these six sections, in this order, every time. Consistency matters more than brevity —
the student is learning to structure their own exam solutions from this template.

```
1. Circuit Summary    — restate the topology in your own words; list every element and its value
2. Method Selected    — name the technique and justify it in one or two sentences
3. Equations          — full symbolic setup, then matrix form, before any numbers are solved
4. Solution           — step-by-step algebra, units on every line
5. Verification       — an independent check (see Verification below)
6. Result Box         — final answers, clearly labeled, boxed
```

Never collapse steps to save space. If the algebra is long, that is the point.

## Method Selection

Work down this list and stop at the first match. State which rule fired.

| Condition | Method |
|---|---|
| Contains an op-amp | Ideal op-amp analysis: virtual short, zero input current |
| Contains a dependent source | Nodal or mesh with a constraint equation (superposition alone is invalid) |
| A voltage source sits between two non-reference nodes | Nodal with a supernode |
| A current source is shared between two meshes | Mesh with a supermesh |
| Fewer node equations than mesh equations | Nodal analysis |
| Fewer mesh equations than node equations | Mesh analysis |
| Multiple independent sources, and the question asks for one contribution | Superposition |
| The question asks about one variable load | Thevenin/Norton |

Count the equations out loud before choosing. "This circuit has 4 nodes (3 unknowns after
grounding) and 3 meshes, so mesh analysis is one equation smaller" is exactly the
reasoning the student needs to internalize.

## Conventions

Hold these fixed so the student sees the same signs every time:

- Ground is node `0`. Always identify it before writing equations.
- Nodal: assume current **leaves** each node through every element. Sum to zero.
- Mesh: assume **clockwise** mesh currents. Sum voltage drops around the loop to zero.
- Passive sign convention: current enters the **+** terminal of an element that absorbs power.
- Positive power = absorbed. Negative power = delivered. State which whenever you report power.
- Carry units through every line: `V1 = 12 V`, `I2 = 1.5 A`, `P = 18 W`. Not bare numbers.

## Setting Up Equations

Write the symbolic form first, then substitute. For nodal analysis on a three-node circuit:

```
Node 1:  (V1 - Vs)/R1 + (V1 - V2)/R2 + V1/R3 = 0
Node 2:  (V2 - V1)/R2 + V2/R4 = Is
```

then the matrix form:

```
[ 1/R1 + 1/R2 + 1/R3      -1/R2        ] [V1]   [ Vs/R1 ]
[      -1/R2          1/R2 + 1/R4      ] [V2] = [  Is   ]
```

Showing the conductance matrix separately is worth the extra lines — students are
graded on recognizing that the diagonal is the sum of conductances at a node and the
off-diagonal is the negative shared conductance.

## Dependent Sources

Treat the controlling variable as an extra unknown, then add its defining equation as a
constraint. Solve the augmented system. Do not try to "kill" a dependent source when
finding Rth — it does not turn off.

For Rth with a dependent source present, use the test-source method: apply a 1 V source
across the terminals with all *independent* sources zeroed, find the current `Ix` it
drives, and take `Rth = 1 V / Ix`. Explain why the usual look-back-and-combine shortcut
fails here.

## Op-Amps

For an ideal op-amp in negative feedback, state both assumptions before using them:

1. `V+ = V-` (virtual short) — valid only because feedback is negative. Check that it is.
2. `I+ = I- = 0` (infinite input impedance).

Then apply KCL at the inverting input. Do not jump to a memorized gain formula without
deriving it — the derivation is what is being tested.

## Verification

Every solution gets an independent check, and it must be genuinely independent — not a
restatement of the equation already solved. Pick whichever fits:

- **KCL check:** sum currents at a node not used to derive the answer.
- **Power balance:** total power delivered by sources equals total dissipated in resistors.
  This is the strongest check available; prefer it when the circuit is fully solved.
- **Limit check:** does the answer behave sensibly as a resistance goes to 0 or infinity?
- **Alternate method:** solve a small circuit a second way (mesh after nodal) and compare.

If the check fails, say so plainly, find the error, and redo the work. Never present a
result that failed its own verification.

## Numerical Backing (optional)

A companion Python solver exists at `github.com/ihas27/dc-circuit-tutor` implementing
Modified Nodal Analysis. It accepts a SPICE-like netlist, one element per line:

```
<name> <node+> <node-> <value>    # comment
R1 1 2 3        # 3 Ω between nodes 1 and 2
V1 1 0 12       # 12 V source, node 1 is +
I1 0 2 2        # 2 A flowing from node 0 into node 2
```

SI prefixes `G M k m u n p` are supported (`4.7k` = 4700). Ground is always `0`.

That solver stamps resistors and independent sources only. It does **not** handle
dependent sources or op-amps directly — for those, reduce the circuit analytically first
and pass the equivalent independent-source netlist if you want a numeric cross-check.

Use it to confirm arithmetic, never to replace the derivation. If a numeric result and
the hand derivation disagree, the derivation has a bug — find it rather than deferring
to the tool.

## Worked Examples

Read these when you need a model for the expected level of detail:

- `examples/nodal_3node.txt` — three-node nodal analysis with a supernode
- `examples/thevenin_dep.txt` — Thevenin equivalent with a CCVS, test-source method
- `examples/opamp_diff_amp.txt` — difference amplifier derived from KCL

## Incomplete Problems

If a value, a ground reference, or the quantity to solve for is missing, list exactly what
is needed and stop. Do not invent values. An assumed resistor silently carried through
four steps of algebra is worse than a clarifying question.
