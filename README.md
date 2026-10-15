# AJL-Algorithm

A Qiskit implementation of the Aharonov–Jones–Landau (AJL) algorithm for
approximating the Jones polynomial of a braid closure at a root of unity.

The AJL algorithm gives an additive approximation to the Jones polynomial
evaluated at $e^{2\pi i / k}$ for integer $k$, in time polynomial in the
number of braid strands and the braid word length. This repository contains
a from-scratch Qiskit implementation of the algorithm and its execution on
several simulators and on IBM Quantum hardware.

## What this repository demonstrates

- Construction of the AJL quantum walk operator from the path model of the
  braid group representation.
- Explicit compilation of the controlled $\Phi_v$ gates from the Jones–Wenzl
  projectors and the $\lambda_v$ constants.
- Implementation of the Hadamard test for the real and imaginary parts of the
  Jones polynomial approximation.
- Execution on Qiskit's `Statevector`, `Aer`, `Sampler`, `Estimator`, and
  IBM Runtime primitives.
- Use of IBM Runtime error mitigation (measurement mitigation, Pauli
  twirling, dynamical decoupling) on hardware.

## Background

Let $\rho: B_n \to U(N)$ be the path-model representation of the braid group
$B_n$ at level $k$. For a braid word $b = \sigma_{i_1}^{e_1} \cdots
\sigma_{i_m}^{e_m} \in B_n$, the AJL algorithm estimates

$$
\langle \hat{b} \rangle_k = \frac{1}{\lambda_{\text{total}}}
\sum_{\text{paths}} \lambda_v \, \rho(b)
$$

by preparing a uniform superposition over admissible paths, applying the
controlled braid representation, and estimating the resulting amplitude by
a Hadamard test. The approximation is additive: with $O(\text{poly}(n, m,
1/\epsilon))$ gates, the algorithm returns an estimate within $\epsilon \cdot
\text{poly}(n, m)$ of the true value.

The original reference is:

> D. Aharonov, V. Jones, Z. Landau. *A polynomial quantum algorithm for
> approximating the Jones polynomial.* STOC 2006.
> [arXiv:quant-ph/0511096](https://arxiv.org/abs/quant-ph/0511096)

## Installation

Requires Python 3.10+ and Qiskit 1.0+.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
