# AlphaCT: Annotated Bibliography

A curated collection of foundational papers with analysis of relevance to AlphaCT.

---

## I. RL for Code Optimization

### AlphaDev: Faster Sorting Algorithms via Deep RL
**Mankowitz et al., Nature 2023**  
📄 [Paper](https://www.nature.com/articles/s41586-023-06004-9) | 🌐 [Blog](https://deepmind.google/discover/blog/alphadev-discovers-faster-sorting-algorithms/)

**Key Contributions:**
- Demonstration of RL discovering novel, faster algorithms at assembly level
- Algorithms integrated into LLVM C++ sort library (real-world deployment)
- Outperformed human-engineered baselines on small sorting problems

**Relevance to AlphaCT:**
- Proves feasibility of RL for low-level code optimization
- Demonstrates path from research to production deployment
- Shows assembly-level synthesis is tractable

**Gap We Address:**
- AlphaDev has no security constraints or formal verification
- Cannot handle constant-time requirements
- No adversarial robustness testing across compilers/microarchitectures

---

### STOKE: Stochastic Superoptimization  
**Schkufza et al., ASPLOS 2013**  
📄 [Paper](https://theory.stanford.edu/~aiken/publications/papers/asplos13.pdf)

**Key Contributions:**
- MCMC-based search for optimal instruction sequences
- Formal verification of correctness using SMT solvers
- Achieved significant speedups on compute kernels

**Relevance to AlphaCT:**
- Alternative search strategy to RL (stochastic vs. learned policy)
- Integration of formal verification into optimization loop
- Demonstrates importance of correctness checking

**Gap We Address:**
- STOKE optimizes for single microarchitecture
- No constant-time awareness
- No adversarial robustness component

---

## II. Constant-Time Verification

### Verifying Constant-Time Implementations (ct-verif)
**Almeida et al., USENIX Security 2016**  
📄 [Paper](https://www.usenix.org/system/files/conference/usenixsecurity16/sec16_paper_almeida.pdf)

**Key Quote:**
> "This makes automated verification of constant-time code an essential 
> component for building secure software."

**Key Contributions:**
- Type-based verification of constant-time properties
- Automated tool for checking CT implementations
- Verified real cryptographic code (NaCl, OpenSSL)

**Relevance to AlphaCT:**
- Provides formal foundation for constant-time definition
- Demonstrates automated CT verification is feasible
- Establishes verification as necessary (not optional) for crypto

**Integration in AlphaCT:**
- Static CT checks during RL search (fast pruning)
- Formal verification for final candidates

---

### BINSEC/REL: Constant-Time at Binary Level
**Daniel et al., IEEE S&P 2020**  
📄 [Paper](https://binsec.github.io/assets/publications/papers/2020-sp.pdf) | 📄 [arXiv](https://arxiv.org/abs/1912.08788)

**Key Quote:**
> "We present BINSEC/REL, the first efficient binary-level automatic tool 
> for bug-finding and bounded-verification of constant-time."

**Key Contributions:**
- Binary-level relational verification (critical for real deployments)
- Found bugs in compiled code that source-level tools missed
- Efficient bounded verification approach

**Relevance to AlphaCT:**
- Compiler optimizations can break source-level CT guarantees
- Binary-level checking is essential (not just source/IR)
- Demonstrates feasibility of automated binary CT verification

**Integration in AlphaCT:**
- Final verification pass at binary level
- Checks across multiple compilers/optimization levels

---

## III. Empirical Leakage Testing

### dude, is my code constant time? (dudect)
**Reparaz, Balasch, Verbauwhede, DATE 2017**  
📄 [Paper](https://eprint.iacr.org/2016/1123.pdf) | 💻 [GitHub](https://github.com/oreparaz/dudect)

**Key Quote:**
> "A humble try at determining whether a piece of code runs in constant 
> time or not."

**Key Contributions:**
- Statistical leakage detection via Welch's t-test
- Practical, lightweight testing methodology
- Widely adopted in crypto engineering practice

**Relevance to AlphaCT:**
- Complements formal verification (catches implementation-level leaks)
- Fast enough for in-loop testing
- Provides empirical validation across real hardware

**Integration in AlphaCT:**
- Dynamic reward signal during training
- Final validation across compiler × microarchitecture matrix
- Generates proof artifacts for operators

---

### ctgrind: Constant-Time Checking with Valgrind
**Langley (Google)**  
💻 [GitHub](https://github.com/agl/ctgrind)

**Key Contributions:**
- Dynamic taint analysis for secret-dependent control flow
- Marks secret data and tracks propagation
- Flags violations (secret-dependent branches, memory accesses)

**Relevance to AlphaCT:**
- Runtime validation of CT properties
- Catches subtle violations missed by static analysis
- Low overhead for testing

**Integration in AlphaCT:**
- Additional validation layer
- Test across diverse inputs and scenarios

---

## IV. Adversarial Robustness

### TRADES: Principled Trade-off Between Robustness and Accuracy
**Zhang et al., ICML 2019**  
📄 [Paper](https://proceedings.mlr.press/v97/zhang19p.html) | 📄 [PDF](https://proceedings.mlr.press/v97/zhang19p/zhang19p.pdf)

**Key Quote:**
> "We identify a trade-off between robustness and accuracy that serves 
> as a guiding principle in the design of defenses."

**Key Contributions:**
- Formal characterization of robustness-accuracy tradeoff
- Principled training objective balancing both
- Demonstrated improved certified robustness

**Relevance to AlphaCT:**
- Analogous tradeoff: performance vs. security (CT guarantees)
- Adversarial training methodology applies to our red-team approach
- Importance of formalization (not just empirical defenses)

**Integration in AlphaCT:**
- Theoretical framework for speed-security tradeoff
- Guides reward design (soft performance vs. hard security gates)

---

### Towards Deep Learning Resistant to Adversarial Attacks
**Madry et al., ICLR 2018**  
📄 [Paper](https://arxiv.org/pdf/1706.06083)

**Key Quote:**
> "We use a natural saddle point (min–max) formulation to capture the 
> notion of security against adversarial attacks."

**Key Contributions:**
- Min-max adversarial training framework
- Showed importance of strong adversaries during training
- Achieved state-of-the-art certified robustness

**Relevance to AlphaCT:**
- Self-play as min-max optimization (optimizer vs. red-team)
- Strong adversary (comprehensive compiler/uarch mutations) crucial
- Hardening requires adversarial pressure during training

**Integration in AlphaCT:**
- Red-team agent design inspired by strong adversarial training
- Search over compiler flags, microarchitectures, workloads

---

## V. Post-Quantum Cryptography

### NIST Post-Quantum Cryptography Standards (FIPS 203/204/205)
**NIST, 2024**  
🌐 [Project Page](https://csrc.nist.gov/projects/post-quantum-cryptography) | 📰 [News](https://www.nist.gov/news-events/news/2024/08/nist-releases-first-3-finalized-post-quantum-encryption-standards)

**Finalized Standards:**
- **ML-KEM (Kyber)**: Key encapsulation mechanism (FIPS 203)
- **ML-DSA (Dilithium)**: Digital signature algorithm (FIPS 204)
- **SLH-DSA (SPHINCS+)**: Stateless hash-based signatures (FIPS 205)

**Relevance to AlphaCT:**
- Critical deployment target (governments mandating PQC transition)
- NTT butterflies are performance bottleneck (Kyber/Dilithium)
- Side-channel resistance is essential (implementation attacks real threat)
- Optimizing these = immediate high-impact application

**Priority Targets:**
- Kyber NTT (Number Theoretic Transform)
- Dilithium NTT
- Modular reduction operations

---

## VI. Verified Cryptographic Libraries

### HACL*: A Verified Modern Cryptographic Library
**Zinzindohoué et al., ACM CCS 2017**  
🌐 [Project](https://hacl-star.github.io/) | 📄 [Paper](https://css.csail.mit.edu/6.5660/2024/readings/hacl-star.pdf)

**Key Contributions:**
- Formally verified crypto implementations in F*
- Extracted to C with performance competitive to hand-written code
- Integrated into Firefox, WireGuard, etc.

**Relevance to AlphaCT:**
- Demonstrates verified crypto can be fast enough for production
- Extraction from high-level spec to low-level code
- Provides baseline for comparison

**Difference:**
- HACL* verifies human-written code
- AlphaCT synthesizes fast code under CT constraints

---

### EverCrypt: Fast, Verified, Cross-Platform Cryptographic Provider
**Protzenko et al., 2020**  
📄 [Paper](https://www.andrew.cmu.edu/user/bparno/papers/evercrypt.pdf)

**Key Contributions:**
- Agile cryptography framework (runtime CPU detection)
- Verified across platforms (x86, ARM, etc.)
- Multiplexing between implementations

**Relevance to AlphaCT:**
- Importance of cross-platform verification
- Multi-implementation strategy for performance
- Real-world deployment considerations

---

## VII. Program Synthesis

### Sketch-Guided Program Synthesis
**Solar-Lezama et al., ASPLOS 2008**

**Key Contributions:**
- Template-based synthesis (sketch = partial program)
- SAT/SMT solving for hole-filling
- Successfully synthesized bit-manipulation kernels

**Relevance to AlphaCT:**
- Alternative to RL: constraint-based synthesis
- Template approach limits search space (tractability)
- Formal correctness by construction

**Difference:**
- Sketch is constraint-based (complete search in theory)
- AlphaCT uses learned policy (incomplete but scalable)

---

## VIII. Hardware Security

### Cache-Timing Attacks on AES
**Bernstein, 2005**

**Key Impact:**
- First practical demonstration of cache timing attacks
- Showed table lookups can leak key bits
- Motivated constant-time implementations

**Relevance to AlphaCT:**
- Motivation for CT constraints
- Cache-safe implementations are optimization target

---

## IX. Related Tools & Frameworks

### FaCT: DSL for Constant-Time Programming
**Cauligi et al., PLDI 2017**

**Key Contributions:**
- Domain-specific language enforcing CT at type level
- Compiler checks for CT violations
- Generates efficient constant-time code

**Relevance to AlphaCT:**
- Complementary approach (language-level vs. optimization)
- Could generate training data (correct CT implementations)

---

## X. Gaps AlphaCT Addresses

After reviewing the literature, key gaps emerge:

1. **No RL + CT Integration**: Existing RL optimizers ignore security
2. **No Binary-Level + Empirical Combo**: Tools do one or the other, not both
3. **No Adversarial Robustness**: No work hardens across compilers/microarchitectures
4. **No Explainability**: Optimization results are black boxes
5. **No End-to-End System**: No path from synthesis → verification → deployment

AlphaCT is the first framework addressing all five simultaneously.

---

## Future Reading

- Side-channel attack surveys
- Microarchitectural timing leaks
- Compiler optimization and security
- More RL for systems work

---

Last updated: November 2025