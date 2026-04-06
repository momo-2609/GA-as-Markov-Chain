# Genetic Algorithms as Markov Chains

**Proofs and interactive demonstrations** — companion document for the theoretical treatment of real-valued genetic algorithms as ergodic Markov chains over a bounded domain 𝒟 = [ℓ, u] ⊂ ℝᴹ.

🔗 **[Live demo → momo-2609.github.io/GA-as-Markov-Chain](https://momo-2609.github.io/GA-as-Markov-Chain)**

---

## Overview

This page presents four self-contained interactive sections, each covering a key theoretical result:

| Section | Topic | Key result |
|---|---|---|
| Tournament Selection | Selection probabilities by rank | P_t(i) = C(i−1, k−1) / C(N, k) |
| Crossover Volume | Expected hyperrectangle volume | 𝔼[Vol(R(a,b))] = Vol(𝒟) / 3ᴹ |
| Offspring Kernel | Four-component mixture decomposition | Crossover × mutation expansion |
| Mutation Normaliser | Boundary truncation of Gaussian | Z(z,Σ) factorises over dimensions |

---

## Sections

### 1 · Tournament Selection

The N individuals are ranked by fitness. In a k-tournament, k individuals are drawn uniformly at random without replacement and the highest-ranked one wins.

The selection probability for individual of rank i is:

```
P_t(i) = C(i-1, k-1) / C(N, k)
```

The interactive chart lets you drag N (population size) and k (tournament size) to see how the probability distribution shifts — from uniform (k = 1) to a point mass at the best individual (k = N).

---

### 2 · Expected Volume of the Crossover Hyperrectangle

For parents **a**, **b** drawn i.i.d. from Unif(𝒟), the arithmetic crossover offspring **z** with zʲ = αʲaʲ + (1−αʲ)bʲ is uniformly distributed on the hyperrectangle R(**a**,**b**).

The expected volume of R relative to the full domain satisfies:

```
E[Vol(R(a,b))] / Vol(D) = (1/3)^M
```

This exponential collapse is why **crossover alone cannot guarantee ergodicity** in any dimension, and why mutation (p_m > 0) is a necessary ingredient.

---

### 3 · Four-Component Offspring Kernel

Composing a crossover mixture kernel with a mutation mixture kernel yields four terms:

- **(i)** Crossover then mutate — convolution of uniform and Gaussian kernels
- **(ii)** Crossover only — offspring lies uniformly in R(**a**,**b**)
- **(iii)** Mutation only — truncated Gaussian centred at parent **a**; **this term has full support on 𝒟** and is the engine of irreducibility
- **(iv)** No operator — exact copy of parent **a**

Set p_m = 0 in the demo to see how removing mutation destroys irreducibility.

---

### 4 · Normalising Constant Z(z, Σ)

For diagonal Σ = diag(σ₁², …, σₘ²), the truncated Gaussian normalising constant factorises:

```
Z(z, Σ) = ∏_j [ Φ((u^j − z^j)/σ_j) − Φ((l^j − z^j)/σ_j) ]
```

The heatmap demo lets you drag the mutation centre z across the unit square and observe how Z drops near boundaries — indicating that a fraction of Gaussian mass overshoots the domain and the density must inflate by 1/Z to compensate.

---

## References

- G. Rudolph, *Convergence analysis of canonical genetic algorithms*, IEEE Trans. Neural Networks **5**(1):96–101, 1994.
- M. D. Vose, *The Simple Genetic Algorithm: Foundations and Theory*, MIT Press, 1999.
- S. P. Meyn & R. L. Tweedie, *Markov Chains and Stochastic Stability*, Springer, 1993.

---

## Running locally

No build step needed. Just open `index.html` in a browser:

```bash
git clone https://github.com/momo-2609/GA-as-Markov-Chain.git
cd GA-as-Markov-Chain
open index.html        # macOS
# or: xdg-open index.html   (Linux)
# or: start index.html       (Windows)
```

The page loads Chart.js from a CDN — an internet connection is required for the charts to render.

---

## Tech stack

- Vanilla HTML/CSS/JS — no framework, no build tooling
- [Chart.js 4.4](https://www.chartjs.org/) for bar and log-scale line charts
- Custom canvas heatmap for the Z(z,Σ) visualisation
- [Google Fonts](https://fonts.google.com/) — DM Serif Display + DM Sans + JetBrains Mono
- Deployed via GitHub Pages
