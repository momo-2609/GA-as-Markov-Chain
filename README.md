# 🧬 Genetic Algorithms as Markov Chains

> *Can a population of random guesses actually converge to the right answer? Turns out yes — and there's a beautiful proof for it.*

🔗 **[Play with the live demo →](https://momo-2609.github.io/GA-as-Markov-Chain)**

---

## What is this?

Genetic algorithms (GAs) are a family of optimisation methods loosely inspired by natural selection. You start with a random population of candidate solutions, make them "compete", combine the winners, occasionally mutate the offspring, and repeat. After enough generations, good solutions tend to survive and bad ones die out.

It works surprisingly well in practice. But *why* does it work? Does it always converge? Can it get stuck?

This project is an interactive companion to a formal answer to those questions. The key insight is that **a genetic algorithm running over a bounded continuous domain is a Markov chain** — and classical Markov chain theory (ergodicity, irreducibility, stationary distributions) gives us the tools to rigorously prove convergence guarantees.

The four interactive sections each correspond to one piece of that proof.

---

## The four pieces

### 🏆 Tournament Selection — *who gets to reproduce?*

Before you can combine individuals, you need to pick which ones. Tournament selection works like a mini competition: randomly grab k individuals from the population, and the fittest one wins the right to become a parent.

The cool thing is you can derive an exact formula for how likely each individual is to be selected, based purely on its rank in the population. Crank up k and only the very best individuals get picked (fast convergence, but risky — you might miss good solutions). Keep k low and almost anyone can reproduce (more exploration, slower convergence).

The interactive chart lets you drag both the population size and tournament size to watch the probability distribution reshape in real time.

---

### 📦 Crossover Volume — *how much of the search space does recombination actually cover?*

When two parents a and b "mate", arithmetic crossover produces an offspring z somewhere between them — specifically, z is uniformly distributed inside the hyperrectangle R(a, b) defined by their coordinates.

Here's the catch: in high dimensions, that hyperrectangle is **tiny** relative to the full domain. Specifically:

```
E[Vol(R(a,b))] / Vol(domain) = (1/3)^M
```

So in 10 dimensions, crossover on average covers only 0.002% of the search space. In 20 dimensions: 0.00000003%. The algorithm would be hopelessly stuck if crossover were the only operator.

This is why mutation is not optional — it's the thing that actually lets the algorithm escape and explore.

---

### 🔬 Offspring Kernel — *what's the full probability of landing anywhere?*

The combined effect of crossover and mutation can be written as a single probability density (the "offspring kernel"). When you expand it out, it naturally splits into four terms:

- **Crossover then mutate** — the offspring lands somewhere inside R(a,b) and then gets nudged by a Gaussian
- **Crossover only** — offspring lands uniformly inside R(a,b), no nudge
- **Mutation only** — offspring is a Gaussian perturbation of parent a, ignoring b entirely
- **Neither** — exact copy of parent a (no exploration at all)

The third term — mutation only — is the most important one theoretically. A Gaussian with positive-definite covariance has **full support everywhere**, meaning it assigns nonzero probability to every point in the domain, no matter how far away. This is what makes the Markov chain *irreducible*: from any starting population, you can reach any other population in finite time.

Set p_m = 0 in the demo and the warning box turns red — that irreducibility property immediately disappears.

---

### 🧮 Mutation Normaliser — *what happens at the boundaries?*

When you mutate near the edge of the domain, part of the Gaussian "overshoots" — it assigns probability mass to points outside the valid region. We clip those to keep the algorithm in bounds, but we need to renormalise the density so it still integrates to 1.

The normalising constant Z(z, Σ) measures how much of the Gaussian stays inside the domain. When z is near the centre, Z ≈ 1 (almost nothing escapes). When z is near a corner, Z can drop to 0.25 — meaning 75% of the mass gets clipped and the remaining density inflates fourfold.

The heatmap demo makes this very visual: drag the green dot toward a corner and watch the colour drain, showing how the density distorts near boundaries.

---

## Why does any of this matter?

If you've ever used a GA, a CMA-ES, or any evolutionary strategy, you've been implicitly relying on these properties. This framework tells you:

- **Mutation is load-bearing.** Crossover alone cannot explore the space; it only recombines what's already there. The mutation rate p_m > 0 is the mathematical guarantee that the algorithm can find anything.
- **Tournament size is a dial.** There's a precise tradeoff between selection pressure (converge fast, risk getting stuck) and diversity (explore broadly, converge slowly).
- **The chain is ergodic** — it has a unique stationary distribution it converges to, regardless of where you start. This is the formal sense in which GAs "work".

---

## Running it locally

No install, no build step. Just clone and open:

```bash
git clone https://github.com/momo-2609/GA-as-Markov-Chain.git
cd GA-as-Markov-Chain
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

You'll need an internet connection for the charts (Chart.js loads from a CDN) and the fonts.

---

## References

The theory here draws on three classic references:

- **Rudolph (1994)** — the original convergence proof for canonical GAs. Short paper, dense with ideas.
- **Vose (1999)** — a whole book on the theory of simple GAs, highly recommended if you want to go deeper.
- **Meyn & Tweedie (1993)** — the Markov chain bible. The GA proof is essentially an application of their ergodicity theorems.

---

## Stack

Pure HTML + CSS + JS, no framework. Charts via [Chart.js](https://www.chartjs.org/). The heatmap is hand-rolled on a `<canvas>`. Fonts: DM Serif Display, DM Sans, JetBrains Mono. Deployed with GitHub Pages.
