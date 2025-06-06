### Cybersecurity
#  RISC-V CFI Attack Dataset Generator

This project generates labeled datasets to support the detection of **Control Flow Integrity (CFI)** violations using **RISC-V execution traces**. The output includes both **benign sequences** from real execution and **malicious gadget chains** synthesized to simulate code reuse attacks such as ROP (Return-Oriented Programming), JOP (Jump-Oriented Programming), and COP (Call-Oriented Programming).

---

##  About Control Flow Integrity (CFI)

**Control Flow Integrity (CFI)** is a security mechanism that ensures a program's execution follows only its intended control-flow paths. CFI violations can occur through attacks that hijack execution using short sequences of valid instructions (called **gadgets**) that end in a control-transfer instruction.

Examples of such attacks:
- **ROP**: gadgets ending in `ret`, `jalr ra`, etc.
- **JOP**: gadgets involving indirect jumps (`jalr`, `jr`)
- **COP**: gadgets ending in `call`, `ecall`, etc.

These attacks manipulate program flow **without injecting new code**—instead reusing existing instructions in unintended ways.

---

##  What the Script Does

### 1. Instruction Disassembly (Static)

- Parses raw hex instructions from execution blocks.
- Disassembles them using Capstone into human-readable opcodes.
- Organizes each block with its original instruction addresses and opcodes.
- Output: `blocksdata<software>.pickle`

### 2. Dynamic Control Flow Graph

- Builds an adjacency list (`adjlist`) from observed trace transitions (`from → to`).
- Classifies control-flow types as `diretto` (direct jumps) or `indiretto` (indirect returns/jumps).
- Output: `adjlist<software>.pickle`

### 3. Benign Chain Generation (s1)

- Extracts valid execution chains of 2–3 connected blocks using only `diretto` transitions from the trace.
- These are real control-flow paths executed by the program.
- Output: `s1<software>.pickle`

### 4. Malicious Chain Generation (s2)

- Identifies blocks that match typical patterns of ROP, JOP, or COP gadgets.
- Randomly creates sequences of 3 gadgets that:
  - Do **not** appear in the benign dataset (`s1`)
  - Are **not directly connected** in the observed trace
  - Have **low connectivity**, mimicking stealthy attack paths
- Balanced output:
  - ⅓ ROP chains
  - ⅓ JOP chains
  - ⅓ COP chains
- Output: `s2<software>.pickle`

### 5. Gadget Metadata (Optional)

- For each gadget block found, metadata is extracted to aid interpretability and traceability.
- Output (if implemented): `gadgets_metadata`

---

## 📁 Output Files

| File Name                        | Description                                                  |
|----------------------------------|--------------------------------------------------------------|
| `blocksdata<software>.pickle`    | Dictionary mapping block addresses to disassembled opcodes  |
| `adjlist<software>.pickle`       | Adjacency list of control-flow edges from dynamic trace     |
| `s1<software>.pickle`            | List of benign instruction block sequences (chains)         |
| `s2<software>.pickle`            | List of malicious gadget chains (ROP, JOP, COP mixed)       |
| `unrecognized<software>.txt`     | Instructions that could not be disassembled                 |
| `blocks_data<software>.txt`      | Text dump of all block disassemblies (optional debug)       |
| `gadgets_metadata.json` (opt.)   | Structured gadget info for auditing, if `extract_metadata()` used |

---

## 🧾 Input Format

A single JSON file with two sections:
```json
{
  "blocks": [
    {
      "addr": "0x1000",
      "insns_addr": ["0x1000", "0x1002"],
      "insns_data": ["0x00008067", "0x00000013"]
    }
  ],
  "trace": [
    {"from": "0x1000", "to": "0x1020", "type": "direct_jump_without_linkage"}
  ]
}
