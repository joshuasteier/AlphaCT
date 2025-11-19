# AlphaCT: Constant-Time Cryptographic Code Optimization via RL

> Extending AlphaDev-style reinforcement learning to security-critical code with formal constant-time guarantees, adversarial robustness, and explainable artifacts.

[![Status](https://img.shields.io/badge/status-research%20proposal-blue)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()

## 🎯 The Vision

[AlphaDev](https://www.nature.com/articles/s41586-023-06004-9) demonstrated that RL can discover faster algorithms that ship into production. But cryptographic code requires **constant-time execution** to prevent side-channel attacks—a constraint existing RL superoptimizers don't handle.

**AlphaCT** proposes the first RL framework that co-optimizes for:

- ⚡ **Performance**: Speed, code size, energy efficiency
- 🔒 **Security**: Formal constant-time proofs + empirical leakage testing
- 🛡️ **Robustness**: Adversarial self-play across compilers/microarchitectures  
- 📝 **Explainability**: Proof bundles + analyst-readable explanations for every kernel

## 💡 Why This Matters

### The Gap

**Current state:**
- ✅ RL can find fast code (AlphaDev, STOKE)
- ✅ Formal methods can prove constant-time properties (ct-verif, BINSEC/REL)
- ❌ **But no one combines them in an end-to-end optimization loop**

### The Impact

Critical for:
- 🔐 **Post-quantum cryptography**: Kyber/Dilithium NTT butterflies (FIPS 203/204)
- 🤝 **Secure multiparty computation**: Oblivious sorting, comparison primitives
- 🔑 **Standard crypto libraries**: Constant-time string operations (memcmp, strcmp)
- 🛡️ **Side-channel resistance**: Cache-safe S-boxes, table lookups

## 🔬 Technical Approach (High-Level)

### RL Loop with Security Constraints
```
State: Partial assembly + symbolic CT invariants
Actions: ISA-safe instruction templates (no secret-dependent control/addressing)
Reward: Cycles/byte + μops + code size + energy

Hard Security Gates:
  1. ✅ Functional correctness (symbolic execution)
  2. ✅ Static CT analysis (taint-based rules)
  3. ✅ Binary-level bounded verification (BINSEC/REL-style)
  4. ✅ Dynamic leakage testing (dudect + ctgrind)
```

### Adversarial Self-Play for Robustness

Red-team agent searches for timing leaks by mutating:
- **Compilers**: GCC, Clang, MSVC with various optimization flags
- **Microarchitectures**: Intel Skylake, AMD Zen, ARM Neoverse
- **Input distributions**: Pathological cases, cache contention
- **Workloads**: Stress scenarios for ports, cache, branch predictor

Failures feed back to optimizer → hardened artifacts.

### Explainability Layer

Every optimized kernel ships with:

1. **Proof bundle**
   - Machine-checkable CT certificate
   - dudect statistical test logs (across compilers × microarchitectures)
   - Binary verification results

2. **Human explanations**
   - **Why-CT**: How we know control flow is secret-independent
   - **Why-faster**: Instruction-level attribution of speedup (μops, ILP, port usage)

3. **CI/CD integration**
   - Drop-in GitHub Actions workflow
   - Automated proof checking, benchmarking, leakage testing

## 🎯 Target Kernels (Prioritized)

### P0: High Confidence Targets
- ✅ Oblivious sort (3-8 elements) for MPC protocols
- ✅ Constant-time memcmp/strcmp
- ✅ Cache-safe S-box microkernels

### P1: High Impact Targets  
- 🔥 **Kyber/Dilithium NTT butterflies** (FIPS 203/204)
- 🔥 **Modular reduction** for post-quantum schemes
- 🔥 **Constant-time conditionals** (cmov patterns)

### P2: Exploratory
- ChaCha20 quarter-round
- GHASH polynomial evaluation
- SHA-2/3 compression rounds

## 📊 Success Criteria

### Performance
- ≥10% speedup vs `-O3`/intrinsics on ≥2 microarchitectures
- Within 5% of best hand-tuned baseline (or surpass) on ≥1 microarchitecture
- Report energy efficiency (RAPL measurements)

### Security (Zero Tolerance)
- **Static**: Pass binary-level CT verification (BINSEC/REL-style)
- **Dynamic**: dudect t-statistic below threshold across compiler × CPU matrix
- **Runtime**: ctgrind shows no secret-dependent branches/addresses

### Robustness
- Measurable reduction in timing regressions found by red-team
- Stable performance across compiler flags and microarchitectures

### Trust & Reproducibility
- ≥95% reproducibility of proofs/benchmarks on independent hosts
- Analyst time-to-accept reduced ≥30% with explanation artifacts

## 📚 Full Technical Proposal

📄 **[Read the complete 12-page proposal](proposal/AlphaCT_Technical_Proposal.pdf)**

Includes:
- Detailed literature review & research gaps
- Complete methodology with formal specifications
- Evaluation plan with concrete metrics
- 12-month milestone roadmap
- Risk analysis and mitigation strategies
- Ethical considerations and dual-use safeguards

Also see:
- 📖 [Annotated bibliography](related-work/annotated-bibliography.md)
- 🗺️ [Implementation roadmap](roadmap.md)
- 🤝 [Call for collaborators](call-for-collaborators.md)

## 🏗️ Implementation Roadmap (12 Months)

### Phase 1: Foundation (M1-M4)
**Deliverable**: Scaffold + P0 kernels with full proof bundles

- M1-M2: ISA-safe templates, correctness/perf/leakage harness, static CT rules
- M3-M4: Oblivious sort + CT string ops with ≥10-30% speedups

### Phase 2: Hardening (M5-M7)  
**Deliverable**: Adversarial self-play integration + robustness ablations

- Red-team integration across compilers/microarchitectures
- Quantify reduction in timing regressions

### Phase 3: PQC & Deployment (M8-M12)
**Deliverable**: Production-ready PQC kernels + upstreaming

- M8-M9: Kyber/Dilithium NTT with CT proofs
- M10: Independent lab validation + energy measurements
- M11: Upstream PRs to crypto libraries
- M12: Publication + artifact release

See [detailed roadmap](roadmap.md) for full breakdown.

## 🧑‍🔬 Why I'm Positioned for This

**Relevant Background:**
- 🤖 **ML/RL Research**: Published at AAAI 2024 on adversarial threats in climate AI
- 🔐 **Cryptography/Security**: 
  - RAND Corporation: Research on AI limitations for defense (published)
  - FBI: Honors internship in cybersecurity (malware analysis, network security)
  - Booz Allen Hamilton: APT detection, anomaly detection systems
- 📐 **Formal Methods/Math**: MS in Applied Mathematics (Stony Brook), MS in Physics (Seton Hall)
- 💻 **Systems**: Production ML systems, HPC, distributed training

**Publication Record**: 15+ peer-reviewed papers spanning ML, physics, applied math, and systems

**Gap I Can Fill**: Bridging RL optimization expertise with security domain knowledge and formal verification understanding—exactly what AlphaCT requires.

## 📖 Key References

### RL for Code Optimization
- **AlphaDev**: Mankowitz et al., *Nature* 2023 ([paper](https://www.nature.com/articles/s41586-023-06004-9))
- **STOKE**: Schkufza et al., *ASPLOS* 2013 ([paper](https://theory.stanford.edu/~aiken/publications/papers/asplos13.pdf))

### Constant-Time Verification
- **ct-verif**: Almeida et al., *USENIX Security* 2016 ([paper](https://www.usenix.org/system/files/conference/usenixsecurity16/sec16_paper_almeida.pdf))
- **BINSEC/REL**: Daniel et al., *IEEE S&P* 2020 ([paper](https://binsec.github.io/assets/publications/papers/2020-sp.pdf))

### Leakage Testing
- **dudect**: Reparaz et al., *DATE* 2017 ([GitHub](https://github.com/oreparaz/dudect))
- **ctgrind**: Langley, Valgrind-based CT checking ([GitHub](https://github.com/agl/ctgrind))

### Adversarial Robustness
- **TRADES**: Zhang et al., *ICML* 2019 ([paper](https://proceedings.mlr.press/v97/zhang19p.html))
- **Adversarial Training**: Madry et al., *ICLR* 2018 ([paper](https://arxiv.org/pdf/1706.06083))

See [annotated bibliography](related-work/annotated-bibliography.md) for complete list.

## 📢 Current Status

**Research proposal stage.** I'm seeking:

- 💬 **Feedback** from RL, cryptography, and formal methods communities
- 🤝 **Collaborators** with complementary expertise (see [call for collaborators](call-for-collaborators.md))
- 💼 **Research lab environment** to execute this vision (actively exploring opportunities)

## 🤝 Get Involved

Interested in this research direction? I'd love to hear from you:

- 📧 **Email**: joshsteier@gmail.com
- 🌐 **Website**: [joshuasteier.github.io](https://joshuasteier.github.io)
- 💼 **LinkedIn**: [linkedin.com/in/joshuasteier](https://linkedin.com/in/joshuasteier)
- 📝 **CV**: [Full CV with publications](https://joshuasteier.github.io/cv/)

Whether you have technical feedback, want to collaborate, or are working on related problems—reach out!

## 📜 License

MIT License - Research proposal and future code

## 📝 Citation

If you reference this work:
```bibtex
@misc{steier2025alphact,
  author = {Steier, Joshua},
  title = {AlphaCT: Constant-Time Cryptographic Code Optimization via 
           Reinforcement Learning with Formal Guarantees},
  year = {2025},
  publisher = {GitHub},
  howpublished = {\url{https://github.com/joshuasteier/AlphaCT}},
  note = {Research proposal}
}
```

---

**Built with passion for secure, efficient, and trustworthy AI systems.** 🚀