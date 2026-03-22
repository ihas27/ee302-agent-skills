# EE 302 DC Circuit Solver — AI Skill

An AI-powered skill for **EE 302 (Electric Circuit Theory)** students that solves DC circuit analysis problems step-by-step, showing every equation, substitution, and unit — exactly the way your professor expects to see it.

---

## What Is This?

This is a **Claude Code skill** — a specialized prompt that turns Claude into a structured circuit analysis tutor. Instead of just giving you an answer, it walks through the full EE 302 methodology:

- Sets up KCL/KVL equations in matrix form
- Shows every algebraic step with units
- Verifies the answer with an independent check
- Explains *why* it chose a particular method

Think of it as a knowledgeable study partner available 24/7.

---

## What It Can Solve

| Topic | Examples |
|---|---|
| **Nodal Analysis** | Find node voltages in a resistive network |
| **Mesh Analysis** | Find mesh currents using KVL |
| **Thevenin Equivalent** | Find Vth and Rth at any terminal pair |
| **Norton Equivalent** | Find In and Rth |
| **Superposition** | Decompose multi-source circuits |
| **Dependent Sources** | VCVS, CCCS, VCCS, CCVS |
| **Op-Amp Circuits** | Inverting, non-inverting, summing, difference amplifier |
| **Power Analysis** | Compute P absorbed/delivered by any element |

> **DC only.** Inductors are treated as short circuits and capacitors as open circuits (steady-state DC assumption).

---

## Getting Started

### 1. Install Claude Code

If you haven't already, install the Claude Code CLI:

```bash
npm install -g @anthropic-ai/claude-code
```

Then authenticate:

```bash
claude
```

### 2. Clone This Repository

```bash
git clone git@github.com:ihas27/ee302-agent-skills.git
cd ee302-agent-skills
```

### 3. Run the Skill

Start Claude Code inside the repo directory:

```bash
claude
```

The skill is automatically available. Just describe your circuit problem in plain English.

---

## How to Ask a Question

You do not need special syntax — just describe the circuit naturally. Here are some examples:

### Example 1 — Nodal Analysis

```
I have a circuit with a 12V voltage source connected between node 1 and ground,
a 3Ω resistor between node 1 and node 2, a 6Ω resistor between node 2 and ground,
and a 2A current source pointing from ground into node 2. Find V1 and V2.
```

### Example 2 — Thevenin Equivalent

```
Find the Thevenin equivalent seen by the load resistor RL in this circuit:
- 10V source in series with a 5Ω resistor connected to node A
- 2Ω resistor between node A and node B
- RL is connected between node B and ground
```

### Example 3 — Op-Amp

```
I have an inverting amplifier with R1 = 1kΩ and Rf = 10kΩ.
The input voltage is 0.5V. What is Vout?
```

### Example 4 — Mesh Analysis

```
A circuit has two meshes. Mesh 1 has a 9V source and a 3Ω resistor.
Mesh 2 has a 6Ω resistor. A 2Ω resistor is shared between both meshes.
Find the mesh currents.
```

### Trigger Phrases

The skill also activates when you say things like:

- "Solve this circuit..."
- "Find Vout..."
- "What is the node voltage at..."
- "Find the Thevenin resistance..."
- "Apply superposition to..."
- "This is an ideal op-amp circuit..."

---

## What the Output Looks Like

Every response is structured the same way so it's easy to follow:

```
1. Circuit Summary      — the skill restates the topology in its own words
2. Method Selected      — which technique (nodal/mesh/Thevenin/etc.) and why
3. Equations            — full matrix setup before any solving begins
4. Solution             — step-by-step with units on every line
5. Verification         — KCL check, power balance, or limit check
6. Result Box           — final answers clearly labeled
```

The skill will never skip steps or hide algebra. If you are preparing for an exam, you can use the output as a model for how to structure your own solutions.

---

## Tips for Best Results

| Do this | Why |
|---|---|
| Label your nodes and elements | The skill needs names (V1, R1, etc.) to set up equations |
| Specify ground | Nodal analysis requires a reference node |
| State what you want to find | "Find Vout", "Find Rth", "Find I through R2" |
| Include all element values | Missing values will be flagged before solving |
| Mention dependent sources explicitly | e.g., "a VCVS with gain 3V/V controlled by Vx" |

---

## Understanding the Methods

### Which method does it pick?

The skill uses this decision tree automatically:

```
Has an op-amp?      → Op-amp analysis (virtual short + zero input current)
Has dependent source? → Nodal or Mesh (superposition not valid alone)
More meshes than nodes? → Nodal Analysis
More nodes than meshes? → Mesh Analysis
```

### Thevenin vs Norton

Both are always available. The skill computes Vth and Rth, then derives:

```
In = Vth / Rth
```

It chooses the Rth method based on whether dependent sources are present:
- **No dependent sources** → kill sources, find R by inspection
- **Dependent sources present** → apply test source method (Vx/Ix)

---

## Example Problems to Try

Copy and paste any of these to get started:

**1. Simple voltage divider**
```
A 24V source is connected in series with a 4Ω and 8Ω resistor.
Find the voltage across the 8Ω resistor using nodal analysis.
```

**2. Two-mesh circuit**
```
Mesh 1: 18V source, 6Ω resistor.
Mesh 2: 12Ω resistor.
Shared element: 3Ω resistor between mesh 1 and mesh 2.
Find both mesh currents and the power delivered by the source.
```

**3. Thevenin with dependent source**
```
Find the Thevenin equivalent at terminals a-b.
A 2A current source is in parallel with a 4Ω resistor (this is the left branch).
A dependent voltage source 2*Ix (where Ix is the current through the 4Ω) is in series
with a 1Ω resistor connected to terminal a. Terminal b is at ground.
```

**4. Non-inverting op-amp**
```
Design a non-inverting amplifier with a gain of 5.
If R1 = 2kΩ, what should Rf be? What is Vout if Vin = 1.5V?
```

---

## Limitations

- **DC steady-state only** — no AC, phasors, or transient analysis
- **Ideal op-amps only** — finite gain bandwidth, slew rate, and rail saturation are not modeled
- **Planar circuits** — mesh analysis requires a planar (non-crossing) circuit topology
- **Linear circuits only** — diodes, transistors, and nonlinear elements are not supported

---

## Repository Structure

```
ee302-agent-skills/
├── README.md
└── .agents/
    └── skills/
        └── dc-circuit-solver/
            ├── SKILL.md                  # The skill definition
            └── examples/
                ├── nodal_3node.txt       # 3-node nodal analysis example
                ├── thevenin_dep.txt      # Thevenin with dependent source
                └── opamp_diff_amp.txt    # Difference amplifier example
```

---

## Contributing

Found a bug or want to add a new example? Open an issue or pull request at [github.com/ihas27/ee302-agent-skills](https://github.com/ihas27/ee302-agent-skills).

Good contributions include:
- New worked example problems (with solutions for verification)
- Edge cases that the skill handles incorrectly
- Additional topology templates (bridge circuits, ladder networks, etc.)

---

## License

MIT — free to use, share, and modify for educational purposes.
