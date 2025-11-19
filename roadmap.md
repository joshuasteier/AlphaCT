# AlphaCT Implementation Roadmap

12-month plan for delivering constant-time cryptographic kernels via RL optimization with formal guarantees.

---

## Phase 1: Foundation (Months 1-4)

### Milestone 1-2: Build the Scaffold (8 weeks)

**Goal**: Working infrastructure for CT-constrained RL optimization

#### Deliverables

**1. ISA-Safe Instruction Templates**
- x86-64 (AVX2, AVX-512)
- ARMv8 (NEON)
- Template library with CT-safe instructions only:
  - ✅ Allowed: cmov, arithmetic, bitwise, SIMD lanes
  - ❌ Forbidden: conditional branches on secrets, secret-dependent addressing

**2. Correctness Harness**
- Symbolic execution for functional equivalence
- Test against golden reference implementations
- Property-based testing framework

**3. Performance Measurement**
- Cycle counters (RDTSC, CNTVCT)
- μop analysis (Intel IACA, llvm-mca)
- Port utilization profiling
- Code size metrics

**4. Static CT Analysis**
- Taint-based rules (secret propagation tracking)
- Control-flow independence checks
- Address independence checks
- Fast pruning during RL search

**5. Dynamic Leakage Testing**
- dudect integration (Welch's t-test)
- ctgrind-style taint tracking
- Test harness for multiple inputs

**6. Initial RL Framework**
- State representation (partial assembly + CT invariants)
- Action space (ISA-safe templates)
- Reward function (cycles + μops + CT penalties)
- PPO/A3C baseline

**Success Criteria:**
- [ ] Can generate random CT-compliant assembly snippets
- [ ] Correctness harness catches functional bugs
- [ ] dudect runs across 3 CPUs
- [ ] RL agent learns to reduce cycles on toy problem

**Estimated Effort:** 320 hours (2 people × 8 weeks)

---

### Milestone 3-4: P0 Kernels (8 weeks)

**Goal**: First production-quality CT kernels with speedups and proofs

#### Target Kernels

**1. Oblivious Sort**
- sort-3 (3 elements)
- sort-5 (5 elements)  
- sort-8 (8 elements)

**Why**: Essential for secure multiparty computation, well-defined problem

**2. Constant-Time String Operations**
- `ct_memcmp` (byte-by-byte comparison)
- `ct_strcmp` (null-terminated strings)
- `ct_memchr` (length-hiding scan)

**Why**: Fundamental primitives, high usage, easy to verify

**3. Cache-Safe S-box Lookup**
- AES S-box (if not AESNI-dominated)
- Generic 8×8 → 8 S-box

**Why**: Classic side-channel target, microbenchmark clarity

#### Success Criteria (Per Kernel)

**Performance:**
- [ ] ≥10% speedup vs `-O3` baseline on ≥2 microarchitectures
- [ ] ≥20% speedup vs naïve constant-time baseline
- [ ] Competitive with best hand-tuned implementation (within 5%)

**Security:**
- [ ] Pass static CT analysis (no taint violations)
- [ ] Pass binary-level bounded verification (BINSEC/REL-style)
- [ ] dudect |t| < 4.5 across 3 CPUs × 2 compilers
- [ ] ctgrind shows no secret-dependent branches/addresses

**Robustness:**
- [ ] Performance stable across GCC/Clang/MSVC
- [ ] No timing regressions on Intel/AMD/ARM

**Artifacts:**
- [ ] Source code (C + inline assembly)
- [ ] Compiled binaries (all compilers × optimization levels)
- [ ] Proof bundle (CT certificate + dudect logs)
- [ ] Benchmark results (CSV + graphs)
- [ ] README with reproduction instructions

**Estimated Effort:** 320 hours (2 people × 8 weeks)

---

## Phase 2: Adversarial Hardening (Months 5-7)

### Milestone 5-7: Self-Play Integration (12 weeks)

**Goal**: Red-team agent that hardens kernels via adversarial search

#### Red-Team Agent Design

**Search Space:**
1. **Compilers**: GCC 11-14, Clang 14-18, MSVC 19
2. **Optimization flags**: `-O2`, `-O3`, `-Ofast`, `-Os`, LTO, PGO
3. **Microarchitectures**: 
   - Intel: Skylake, Ice Lake, Sapphire Rapids
   - AMD: Zen 2, Zen 3, Zen 4
   - ARM: Cortex-A72, Neoverse V1/N2
4. **Input distributions**: Uniform, adversarial, cache-hostile
5. **Stress scenarios**: Cache thrashing, port contention, branch mispredictions

**Objective**: Find compiler/uarch/input combinations that cause:
- Timing leaks (dudect detects)
- Performance regressions (>10% slowdown)
- Functional bugs

**Training Loop:**
```
Optimizer generates candidate kernel
  ↓
Red-team searches for failures (parallel)
  ↓
Failures feed back as negative examples
  ↓
Optimizer learns to avoid failure modes
```

#### Evaluation

**Ablation Study:**
Compare optimization with vs. without red-team:

**Metrics:**
- Number of regressions found by held-out test suite
- Performance variance across compilers/microarchitectures
- Failure rate under adversarial inputs

**Hypothesis:**
Self-play reduces regressions by ≥30% vs. optimization-only baseline.

**Success Criteria:**
- [ ] Red-team discovers ≥5 timing leaks in naïve optimized code
- [ ] Self-play training reduces leaks to 0 on held-out test set
- [ ] Hardened kernels show ≤5% performance variance across targets
- [ ] Published ablation study quantifying robustness gains

**Estimated Effort:** 480 hours (2 people × 12 weeks)

---

## Phase 3: PQC & Deployment (Months 8-12)

### Milestone 8-9: Post-Quantum Kernels (8 weeks)

**Goal**: Optimize NIST-standardized PQC hot paths

#### Target Kernels

**1. Kyber NTT (FIPS 203)**
- Forward NTT (time-domain → frequency-domain)
- Inverse NTT
- Butterfly operations (core building block)

**2. Dilithium NTT (FIPS 204)**
- Similar to Kyber but different parameters
- Modular reduction variants

**3. Modular Reduction**
- Barrett reduction
- Montgomery reduction
- For q = 3329 (Kyber), q = 8380417 (Dilithium)

**Challenges:**
- Larger code (100-200 instructions vs. 10-20 for sort)
- More complex correctness (number-theoretic properties)
- Higher security scrutiny (NIST standards compliance)

**Success Criteria:**
- [ ] ≥15% speedup on Kyber NTT vs. reference implementation
- [ ] ≥15% speedup on Dilithium NTT vs. reference implementation
- [ ] Pass CT verification across GCC/Clang on x86 + ARM
- [ ] dudect validation on ≥3 microarchitectures
- [ ] Energy efficiency improvements (RAPL measurements)

**Estimated Effort:** 320 hours (2 people × 8 weeks)

---

### Milestone 10: Independent Validation (4 weeks)

**Goal**: External reproduction and validation

#### Tasks

**1. Independent Lab Testing**
- Package artifacts for easy reproduction
- Distribute to 2-3 external labs/researchers
- Collect validation results

**2. Energy Measurements**
- RAPL (Running Average Power Limit) profiling
- Performance-per-watt metrics
- Compare to baselines

**3. Extended Compiler Testing**
- Intel ICC
- Additional ARM compilers
- Newer GCC/Clang versions

**Success Criteria:**
- [ ] ≥95% reproducibility rate across independent hosts
- [ ] No new timing leaks found by external testers
- [ ] Energy improvements documented

**Estimated Effort:** 160 hours (2 people × 4 weeks)

---

### Milestone 11: Upstreaming (4 weeks)

**Goal**: Integrate optimized kernels into real cryptographic libraries

#### Target Repositories

**Options:**
1. **libsodium**: Popular crypto library (NaCl successor)
2. **liboqs**: Open Quantum Safe (PQC reference)
3. **OpenSSL**: Industry standard (harder to upstream)
4. **Kyber/Dilithium reference implementations**: Official NIST repos

**Process:**
1. Open GitHub issue proposing optimization
2. Submit pull request with:
   - Code (with clear CT comments)
   - Proof bundle
   - Benchmark results
   - CI integration instructions
3. Address review feedback
4. Iterate until merge

**Success Criteria:**
- [ ] ≥1 PR accepted into credible crypto library
- [ ] Code reviewed by domain experts
- [ ] CI passing with CT checks enabled

**Estimated Effort:** 160 hours (2 people × 4 weeks)

---

### Milestone 12: Publication & Release (4 weeks)

**Goal**: Disseminate research and release artifacts

#### Deliverables

**1. Research Paper**
- Target venue: IEEE S&P, USENIX Security, or ICML
- 12-14 pages + appendix
- Includes all evaluation results, ablations, case studies

**2. Open-Source Release**
- GitHub repository with:
  - Complete framework code
  - Trained models (gated release if necessary)
  - All kernel artifacts
  - Reproduction scripts
  - Documentation

**3. Proof Bundles**
- CT certificates for each kernel
- dudect logs (raw data)
- Binary verification results
- Benchmark data (CSV)

**4. CI/CD Integration**
- GitHub Actions workflow
- GitLab CI template
- Docker containers for reproducible builds

**5. Model & System Cards**
- Capabilities and limitations
- Intended use cases
- Ethical considerations
- SBOM (Software Bill of Materials)

**6. Blog Posts / Talks**
- Technical deep-dive blog post
- Conference presentation (if accepted)
- Community engagement

**Success Criteria:**
- [ ] Paper submitted to top venue
- [ ] Code released under permissive license
- [ ] ≥50 GitHub stars in first month
- [ ] Artifacts used by ≥3 external researchers/teams

**Estimated Effort:** 160 hours (2 people × 4 weeks)

---

## Resource Requirements

### Team

**Minimum:**
- 1 RL/ML researcher (algorithm design, training)
- 1 cryptographic engineer (CT verification, domain expertise)

**Ideal:**
- +1 systems engineer (compilers, microarchitecture)
- +1 formal methods expert (verification integration)

### Compute

**Training:**
- 8× NVIDIA A100 GPUs (or equivalent) for RL training
- ~100K GPU-hours estimated

**Evaluation:**
- Access to diverse CPU testbeds:
  - Intel: Skylake, Ice Lake, Sapphire Rapids
  - AMD: Zen 2, Zen 3, Zen 4
  - ARM: Neoverse V1, N2

### Software Infrastructure

- Symbolic execution engine (KLEE, angr, or custom)
- Binary analysis tools (BINSEC or build own)
- Compiler toolchains (GCC, Clang, MSVC)
- Benchmark frameworks (Google Benchmark, LLVM-MCA)

---

## Risk Mitigation

### Technical Risks

**1. Verifier Bottlenecks**
- **Risk**: CT verification too slow for RL loop
- **Mitigation**: Staged checks (fast taint pruning → full verification on finalists)

**2. Compiler/Microarchitecture Leaks**
- **Risk**: Optimizations break CT at binary level
- **Mitigation**: Binary-level verification + extensive testing matrix

**3. Overfitting to Benchmarks**
- **Risk**: Speedups only on specific inputs/CPUs
- **Mitigation**: Require wins on ≥2 diverse microarchitectures + energy metrics

**4. Search Space Too Large**
- **Risk**: RL can't find good solutions
- **Mitigation**: Start with small kernels (sort-3), use ISA-safe templates

### Organizational Risks

**1. Upstream Rejection**
- **Risk**: Crypto maintainers don't accept optimizations
- **Mitigation**: Early engagement, provide proofs/explanations, target multiple repos

**2. Resource Constraints**
- **Risk**: Insufficient compute/team
- **Mitigation**: Prioritize P0 kernels first, publish incremental results

---

## Success Metrics Summary

### Must-Have (M12)
- [ ] ≥3 P0 kernels with ≥10% speedups and full CT proofs
- [ ] ≥1 PQC kernel (Kyber or Dilithium) optimized
- [ ] Open-source framework released
- [ ] Paper submitted to top venue

### Should-Have (M12)
- [ ] ≥1 upstream PR merged
- [ ] Self-play demonstrably reduces regressions
- [ ] Independent validation by external lab

### Stretch Goals
- [ ] Paper accepted at IEEE S&P / USENIX Security
- [ ] Adopted by ≥2 production crypto libraries
- [ ] Follow-on work from other researchers

---

**Timeline**: 12 months | **Estimated Total Effort**: 1,920 hours  
**Last Updated**: November 2025