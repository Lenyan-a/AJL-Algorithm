# AJL-Algorithm

A Qiskit implementation of the Aharonov–Jones–Landau (AJL) algorithm for
approximating the Jones polynomial of a braid closure at a root of unity.

The AJL algorithm gives an additive approximation to the Jones polynomial
evaluated at $q = e^{2\pi i / k}$ for integer $k$, in time polynomial in the
number of braid strands and the braid word length. This repository contains a
from-scratch Qiskit implementation of the algorithm and its execution on
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
- Use of IBM Runtime error mitigation (measurement mitigation, Pauli twirling,
  dynamical decoupling) on hardware.

## Background

Let $\rho: B_n \to U(N)$ be the path-model representation of the braid group
$B_n$ at level $k$. For a braid word
$b = \sigma_{i_1}^{e_1} \cdots \sigma_{i_m}^{e_m} \in B_n$, the AJL algorithm
estimates

$$
\langle \hat{b} \rangle_k = \frac{1}{\lambda_{\text{total}}}
\sum_{\text{paths}} \lambda_v \, \rho(b)
$$

by preparing a uniform superposition over admissible paths, applying the
controlled braid representation, and estimating the resulting amplitude via a
Hadamard test. The approximation is additive: with $O(\mathrm{poly}(n, m,
1/\epsilon))$ gates, the algorithm returns an estimate within
$\epsilon \cdot \mathrm{poly}(n, m)$ of the true value.

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
```

To run the IBM Runtime notebook, you additionally need an IBM Quantum account
and an API token. See the
[IBM Quantum documentation](https://docs.quantum.ibm.com/) for setup.

## Usage

The main implementation is in `AJL_Algorithm.ipynb`. It runs end-to-end on the
local statevector simulator and produces an estimate of the Jones polynomial
at a chosen level $k$ for a chosen braid word.

To reproduce the hardware execution, open `AJL_Algorithm_Hardware.ipynb`. This
notebook submits the same circuits to an IBM Quantum backend using
`qiskit-ibm-runtime`, with error mitigation enabled.

The `legacy/` folder contains earlier iterations that were used to compare
execution on different primitives (sampler, estimator, statevector, Aer).
They are kept for reference but are not maintained.

## Repository structure

```
.
├── AJL_Algorithm.ipynb          # main implementation (statevector)
├── AJL_Algorithm_Hardware.ipynb # IBM Runtime execution with error mitigation
├── legacy/                      # earlier notebook variants (sampler, estimator, etc.)
├── results/                     # sample outputs from hardware runs
├── requirements.txt
├── LICENSE
└── README.md
```

## Implementation notes

### Conventions

- Braid group generators $\sigma_i$ act on strand $i$ and strand $i+1$, with
  the standard Artin presentation:
  $\sigma_i \sigma_{i+1} \sigma_i = \sigma_{i+1} \sigma_i \sigma_{i+1}$ and
  $\sigma_i \sigma_j = \sigma_j \sigma_i$ for $|i - j| \geq 2$.
- The level $k$ determines the quantum group $U_q(\mathfrak{sl}_2)$ with
  $q = e^{2\pi i / k}$ and the Jones–Wenzl idempotents $f^{(k)}_n$ that
  define the path model.
- The path register is encoded in binary, and $\Phi_v$ is implemented as a
  controlled modular addition.

### Default parameters

The main notebook runs the AJL algorithm for:

- Braid group: $B_6$
- Braid word: $\sigma_4 \sigma_4 \sigma_2 \sigma_2$ (Hopf 3-chain)
- Level: $k = 5$

These parameters can be changed at the top of the notebook. Larger $n$ and $k$
increase the path-register width and gate depth.

## Validation

The statevector implementation is checked against the exact Jones polynomial
evaluated at $q = e^{2\pi i / k}$ for small braids, where the exact value can
be computed by direct matrix multiplication of the path-model representation.
The test suite in `tests/` covers:

- Unitarity of the controlled braid gates.
- Agreement of the Hadamard test with the exact real and imaginary parts for
  the Hopf 3-chain at $k = 5$.
- Reproducibility of the statevector result across runs.

## Results

Sample outputs from the hardware run are in `results/`. The hardware estimate
is consistent with the statevector estimate within shot noise and the additive
error guaranteed by the AJL theorem.

## Limitations

- The AJL algorithm gives an **additive** approximation. The error scales
  polynomially in $n$ and the braid word length, and it is not a
  multiplicative or relative approximation.
- The path register width grows with $n$ and $k$. For the values in this
  repository the circuit is small enough for statevector simulation, but
  hardware execution is limited to shallow braids and low $k$.
- The current implementation does not use ancilla-efficient compilation of
  the Jones–Wenzl projectors; the controlled $\Phi_v$ gates are built
  directly from the path-model representation.

## References

1. D. Aharonov, V. Jones, Z. Landau. *A polynomial quantum algorithm for
   approximating the Jones polynomial.* STOC 2006.
   [arXiv:quant-ph/0511096](https://arxiv.org/abs/quant-ph/0511096)
2. M. H. Freedman, A. Kitaev, M. J. Larsen, Z. Wang. *Topological quantum
   computation.* Bull. Amer. Math. Soc. 40 (2003), 31–38.
3. V. F. R. Jones. *Hecke algebra representations of braid groups and link
   polynomials.* Ann. of Math. 126 (1987), 335–388.
4. Qiskit documentation: [https://docs.quantum.ibm.com/](https://docs.quantum.ibm.com/)

## License

MIT. See `LICENSE` for details.

## Author
[github.com/Lenyan-a](https://github.com/Lenyan-a)
