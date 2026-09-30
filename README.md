# Learning With Errors (LWE) — implementation and experimental analysis
🇫🇷 A French version of the notebook is available: [LWE_FR.ipynb](LWE_FR.ipynb)

> A *from scratch* implementation of a symmetric cryptosystem based on the **Learning With Errors** problem, with an empirical study of the impact of the parameters ($\sigma$, $m$, $n$, $q$) on decryption reliability and security.

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![NumPy](https://img.shields.io/badge/NumPy-✓-013243)
![License](https://img.shields.io/badge/License-MIT-green)

*Personal learning project.*

---

## Context

Current public-key cryptosystems (RSA, ECC) rely on the hardness of factorization and of the discrete logarithm; two problems that Shor's algorithm breaks in polynomial time on a quantum computer. Post-quantum cryptography looks for problems that resist these attacks: **LWE** (Regev, 2005) is one of its pillars, and it is the basis of the NIST standard **ML-KEM / Kyber** (FIPS 203).

This notebook builds, step by step and without any cryptographic library, a secret-key LWE instance, then experimentally explores the boundary between **enough noise to hide the secret** and **too much noise to decrypt**.

## Notebook contents

| Part | Topic |
|---|---|
| **1. Introduction** | The quantum threat, vulnerability of RSA/ECC, emergence of LWE |
| **2. Building blocks** | Discrete Gaussian sampling, key generation, encryption/decryption of one bit |
| **3. Experimental validation** | Test on a single instance, then on 100 messages |
| **4. Parametric analysis** | Phase transitions depending on $\sigma$, $m$, $n$, $q$; cross analysis of $(m, \sigma)$ with a heatmap |
| **5. Summary & outlook** | RSA vs LWE comparison, limitations, outlook on Ring-LWE / Kyber |

## Key results

- **Noise is necessary**: with $\sigma = 0$, the system is solved instantly by Gaussian elimination. The Gaussian noise turns a bell-shaped distribution (on $e$) into an almost uniform distribution (on $b$), which hides the secret.
- **Clear phase transition on $m$** (number of equations): below a threshold, the success rate is unstable; above it, the redundancy makes it possible to tolerate more noise.
- **$n$ affects security, not reliability**: varying the dimension of the secret leaves the decryption curves almost overlapping; $n$ is the security parameter.
- **$q$ determines the noise tolerance**: a large modulus "gives room" to the noise before it crosses the decision threshold $\lfloor q/4 \rfloor$.

## How to run

### Online (recommended, no installation)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/anisadje/learning-with-errors/blob/main/LWE_EN.ipynb)

### Locally
```bash
git clone https://github.com/anisadje/learning-with-errors.git
cd learning-with-errors
pip install -r requirements.txt
jupyter notebook LWE_EN.ipynb
```

## Known limitations & next steps

- **Toy parameters**: the chosen values ($n=2$, $q=11$) are meant for visualization and do not represent real security; the secret space ($q^n = 121$) can be brute-forced trivially.
- **Decryption failures**: ~2% observed with some settings.
- **Symmetric variant**: this project implements secret-key LWE, not the full public-key scheme.
- **Outlook**: the quadratic size of the matrix $A$ motivates moving to **Ring-LWE** ($\mathbb{Z}_q[X]/(X^n+1)$) and to **Kyber/ML-KEM**; a natural direction to extend the project.

## Tech stack

Python · NumPy · Matplotlib · Seaborn

## Main references

Regev (2005), *On lattices, learning with errors…* (JACM) · Shor (1994) · NIST FIPS 203 (2024) · Micciancio, CSE 208 (UC San Diego). *Full bibliography at the end of the notebook.*

## Author

**Anîsa Djedje** — [GitHub](https://github.com/anisadje)
