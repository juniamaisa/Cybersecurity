# Cybersecurity Research on Embedded Systems – CRA Detection

This repository contains code and data developed for cybersecurity research focused on detecting **Code Reuse Attacks (CRA)** — such as **ROP**, **JOP**, and **COP** — in **embedded systems**.  
The experiments are based on execution traces of **RISC-V** binaries and the application of **Control Flow Integrity (CFI)** concepts to simulate and detect malicious behavior.

---

## 📁 Repository Structure

- `CRA_generator/`  
  Contains the complete algorithm used to generate simulated attack data.  
  This module uses instruction-level traces and enforces CFI-aware rules to construct benign and malicious instruction chains (gadgets), enabling the creation of labeled datasets for machine learning.

- `Data_extracted/`  
  Includes raw data extracted directly from the execution of **RISC-V** binaries using **QEMU** (`qemu-riscv64`, version 9.2.3).  
  The data contains instruction-level traces and control-flow transitions used in the simulation and training pipeline.

- `Data_processed/`  
  Includes .pickle dataset generated using the code generator available in `CRA_generator/`.

---

This repository serves as the foundation for experiments involving:

- trace processing  
- CRA chain simulation  
- DNN-based detection  

with a focus on **lightweight** and **generalizable** methods for **embedded environments**.



Author Contact: Júnia Maísa - junia.deoliveira@unibo.it
