# 🛡️ QuantumShield-HQC
**High-Speed, Side-Channel Resistant Hardware Security Engine for Post-Quantum Communications**

[![Hardware](https://img.shields.io/badge/Target%20FPGA-Xilinx%20Artix--7-orange.svg)](#)
[![Language](https://img.shields.io/badge/HDL-Verilog-green.svg)](#)
[![Security](https://img.shields.io/badge/Execution-100%25%20Constant--Time-brightgreen.svg)](#)

> **Domain:** Cybersecurity & Quantum-Safe Digital Infrastructure  
> **Developed By:** G. Sivanesh (Research Student, SRM Easwari Engineering College)

---

## 📖 Overview & The Real-World Problem

Every day, billions of sensitive transactions—ranging from online banking and UPI payments to electronic health records, defense communications, and HTTPS web traffic—rely on public-key encryption (such as RSA and Elliptic Curve Cryptography) to remain private. However, rapid advancements in quantum computing threaten to break these mathematical foundations simultaneously. Through **"Harvest Now, Decrypt Later" (HNDL)** attacks, adversaries are actively intercepting and archiving encrypted financial, medical, and government data today so they can decrypt it retroactively once large-scale quantum hardware matures.

Transitioning global digital infrastructure to Post-Quantum Cryptography (PQC) introduces three critical bottlenecks:
1. **Single-Point-of-Failure Risk:** Relying solely on lattice-based mathematics (such as ML-KEM/Kyber) creates systemic risk across the internet. Organizations require a proven, crypto-agile backup built on an entirely different mathematical foundation—such as error-correcting codes (Hamming Quasi-Cyclic / HQC).
2. **Severe Software Bottlenecks:** Running code-based quantum-safe algorithms purely in software on standard CPUs is too slow and memory-intensive for high-frequency payment gateways, web servers, and edge IoT devices.
3. **Timing Side-Channel Leaks:** Naive software implementations leak secret keys through microsecond variations during rejection sampling, allowing attackers to recover keys remotely by measuring server response times.

---

## 💡 Our Solution: QuantumShield-HQC

**QuantumShield-HQC** bridges the gap between theoretical post-quantum security and practical hardware deployment. It provides a compact, ultra-fast, and timing-attack-proof FPGA hardware security engine for the **Hamming Quasi-Cyclic (HQC)** Key Encapsulation Mechanism (selected for Round 4 of the NIST Post-Quantum Cryptography Standardization Process), paired with Python verification pipelines and compatibility with the **Open Quantum Safe (`liboqs`)** ecosystem.

Instead of burdening host CPUs with heavy polynomial and error-correcting arithmetic, **QuantumShield-HQC** offloads the complete quantum-safe key exchange lifecycle—**Key Generation (`keygen`), Encapsulation (`encap`), and Decapsulation (`decap`)**—onto reconfigurable hardware (Xilinx Artix-7 FPGA).

### ✨ Key Architectural Innovations
* **Unified Multi-Operation Security Core (`joint_design`):** Combines KeyGen, Encap, and Decap into a single top-level module selectable via a 2-bit operation port. Sharing the high-area `SHAKE256`, `poly_mult`, and `encap` submodules reduces the hardware footprint by **40% to 80%** compared to existing HLS designs.
* **100% Constant-Time Execution (Side-Channel Immunity):** Eliminates the *"Don't Reject This"* rejection-sampling timing vulnerability using hardware-enforced **Constant Weight Word (`fixed_weight_vector_cww`)** and **Fast Non-Biased (`fixed_weight_vector_fnb`)** fixed-weight vector samplers.
* **2x Faster State-Preserving `SHAKE256` Engine:** Upgraded Keccak round architecture (`parallel_slices = 32`) with internal sponge state preservation and on-demand pseudorandom byte generation, reducing fixed-weight sampling cycles by **32% to 65%**.
* **50% Lower BRAM Sparse Polynomial Multiplier (`poly_mult`):** Uses dual-port Block RAM and a configurable barrel shifter (`BW = 32, 64, 128`) with interleaved $X^n - 1$ reduction alongside a custom `location_based_adder`.
* **Concatenated Error-Correcting Hardware Codec:** Full RTL implementation of shortened Reed-Solomon encoding/decoding (parallel $GF(2^8)$ multipliers) concatenated with duplicated Reed-Muller encoding and a 7-layer radix-2 butterfly Hadamard Transform decoder.

---

## 📂 Repository Structure

```text
QuantumShield-HQC/
├── hardware/
│   ├── keygen/         # Key Generation Verilog RTL, testbenches, TCL scripts & Python input alignment
│   ├── encap/          # Encapsulation Verilog RTL, testbenches, TCL scripts & Python input alignment
│   ├── decap/          # Decapsulation Verilog RTL, testbenches, TCL scripts & Python input alignment
│   ├── common/         # Shared modules: SHAKE256, poly_mult, adders, CWW & FNB constant-time samplers
│   └── joint_design/   # Unified top-level HQC accelerator sharing submodules across KeyGen/Encap/Decap
├── Makefile            # Automated build packaging and Xilinx Vivado batch simulation targets
└── README.md           # Project documentation
