# Learning With Errors: encrypt, then break

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/anisadje/learning-with-errors/blob/main/LWE_EN.ipynb)

A from-scratch study of the **Learning With Errors (LWE)** problem in Python, seen from two sides:

1. **The designer:** how to encrypt with LWE, and how to choose the parameters so that decryption works.
2. **The attacker:** how to break LWE with lattice reduction, and when the attack fails.

The common thread is a trade-off: the error $e$ must be **large enough** to hide the secret, but **small enough** for decryption to stay reliable.

> Personal project, Anîsa Djedje, 3rd-year Double Degree in Mathematics & Computer Science, Université Paris-Saclay.

## Context

LWE was introduced by Oded Regev in 2005. Given a public matrix $A \in \mathbb{Z}_q^{m \times n}$ and $b = As + e \bmod q$, where $e$ is a small error drawn from a discrete Gaussian of standard deviation $\sigma$, the problem is to recover the secret $s$.

LWE is believed to be hard even for quantum computers. It is the foundation of **ML-KEM (Kyber)**, the post-quantum key encapsulation standard adopted by NIST in 2024 (FIPS 203).

## Status

| Part | Content | Status |
|---|---|---|
| 1 | LWE encryption and decryption reliability | ✅ Done |
| 2 | Breaking LWE with lattice reduction (LLL) | 🟡 In progress |
| 3 | From LWE to Module-LWE and Kyber | ⬜ Planned |

## Part 1: LWE encryption and decryption reliability ✅

**Implementation** (no cryptographic library, NumPy only):
- `discrete_gaussian`: sampler for the error $e$.
- `encrypt_LWE` / `decrypt_LWE`: secret-key, bit-by-bit scheme. The bit $\text{msg} \in \{0,1\}$ is encoded by adding $\text{msg} \cdot \lfloor q/2 \rfloor$, and decryption compares the result to the threshold $q/4$.
- `phase_transition_study`: decryption success rate as a function of $\sigma$, $m$, $n$ and $q$.

**Key results:**
- **Noise is necessary.** With $\sigma = 0$, Gaussian elimination recovers $s$ immediately.
- **Phase transition.** When the accumulated noise becomes too large, the success rate drops sharply to 50%, i.e. random guessing.
- **$m$:** above a threshold, more equations let the scheme tolerate more noise.
- **$n$:** affects security, but barely changes decryption reliability.
- **$q$:** a larger modulus leaves more room before the noise crosses $q/4$.

**Conclusion:** decryption puts an **upper bound** on the noise $\sigma$.

## Part 2: Breaking LWE with lattice reduction 🟡

**1. Lattices** ✅
- Lattices, bases and determinant; good vs. bad bases, with a proof that two bases generate the same lattice and an illustration.
- LWE as a lattice problem: the vectors $Az \bmod q$ form the lattice $\Lambda_q(A)$, which contains $As$. Recovering $s$ amounts to solving the **Closest Vector Problem (CVP)**.
- Toy example ($q = 17$): a small error keeps $As$ as the closest lattice point to $b$, a large one makes it jump to another point, i.e. a wrong secret.

**2. The LLL algorithm** ✅
- Built step by step, each ingredient motivated by a hand-computed counterexample: why Gram–Schmidt is not enough, size reduction, why swaps are needed, and the **Lovász condition** ($\delta = 3/4$).
- From-scratch implementation of `gram_schmidt`, `lll` and `is_lll_reduced`.
- Tests: hand-computed examples, determinant preservation, random tests in dimension 5.

**3. The attack** 🟡
- ✅ **Kannan's embedding:** turning CVP into SVP by adding $b$ to the basis with an extra coordinate, so that $(e, 1)$ becomes a short lattice vector that LLL can find.
- ✅ **Recovering $s$:** fix the sign, remove the noise ($As = b - e$), solve a linear system mod $q$, check the result.
- ✅ **Full demo on the toy example:** LLL finds $e$, then $s = 3$, from $A$ and $b$ only.
- ⬜ General construction of the basis of $\Lambda_q(A)$ and of the Kannan matrix.
- ⬜ The attack on real instances generated with the Part 1 code.

**4. Experiments** ⬜ Attack success rate as a function of $\sigma$ and $n$, and the threshold beyond which LLL fails.

**5. Conclusion** ⬜ Decryption gives an **upper bound** on $\sigma$, security a **lower bound**. Why Kyber's real parameters ($n = 256$, $q = 3329$) resist LLL.

## Part 3: Kyber / ML-KEM ⬜

- From LWE to **Module-LWE**, over the ring $R_q = \mathbb{Z}_q[x]/(x^n + 1)$.
- Structure of Kyber and its parameter choices.

## Limitations

- The toy parameters (e.g. $n = 2$, $q = 11$) are chosen for visualization and are trivially brute-forced. They are not secure settings.
- The scheme in Part 1 is a secret-key version of LWE encryption, not Regev's public-key scheme.

## Running the notebooks

**Online:** click the *Open in Colab* badge above.

**Locally:**
```bash
git clone https://github.com/anisadje/learning-with-errors.git
cd learning-with-errors
pip install -r requirements.txt
jupyter notebook LWE_EN.ipynb
```
A French version is also available: `LWE_FR.ipynb`.

**Stack:** Python, NumPy, Matplotlib, Seaborn, Jupyter.

## References

1. O. Regev, *On lattices, learning with errors, random linear codes, and cryptography*, STOC 2005.
2. A. K. Lenstra, H. W. Lenstra Jr., L. Lovász, *Factoring polynomials with rational coefficients*, Mathematische Annalen 261 (1982).
3. R. Kannan, *Minkowski's convex body theorem and integer programming*, Mathematics of Operations Research 12(3) (1987).
4. P. W. Shor, *Algorithms for quantum computation: discrete logarithms and factoring*, FOCS 1994.
5. NIST, *FIPS 203: Module-Lattice-Based Key-Encapsulation Mechanism Standard*, 2024.
6. D. Micciancio, *CSE 208: Advanced Cryptography (lattices)*, UC San Diego, lecture notes.

## Author

Anîsa Djedje · [LinkedIn](https://www.linkedin.com/in/anisa-djedje/)
