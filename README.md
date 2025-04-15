# Variational Quantum Eigensolvers - The Levy-Lieb Procedure

This repository contains code for calculating the Levy-Lieb density functional, $F_{LL}[n]$ of a Hubbard dimer using a density-constrained Variational Quantum Eigensolver (VQE) and subsequently utilizing $F_{LL}[n]$ in the calculation of the ground energy. Following this, the Levy-Lieb quantum kernel is defined and utilised, to demonstrate its efficacy in calculating $F_{LL}[n]$. This work follows closely the theory and implementation outlined in Ref. 1 and attempts to reproduce the main results.

## Packages Used
 - qiskit 0.46.0
 - qiskit_nature 0.7.1
 - qiskit_algorithms 0.2.2
 - scikit-learn 1.12.0
 - numpy 1.23.5
 - matplotlib 3.8.0
 - scipy 1.12.0

Alternatively, the packages used can run by:
```bash
pip install -r requirements.txt
```

## Theoretical Background

### The Fermi Hubbard Model

The Fermi-Hubbard Model is a lattice model in which electrons experience an interaction potential, on-site potential and can tunnel between lattice sites. Each site has two spin orbitals, corresponding to up and down. Therefore, each site can possess a maximum of two electrons. In this project, we consider the two site Fermi-Hubbard lattice whose hamiltonian is as follows:

```math
\hat{H} = -t \sum_{\sigma=\uparrow,\downarrow} (c^\dagger_{1\sigma} c_{2\sigma} + \text{h.c.}) + U \sum_{i} \hat{n}_{i\uparrow} \hat{n}_{i\downarrow} + \sum_{i} v_i (\hat{n}_{i\uparrow} + \hat{n}_{i\downarrow}) 
```
Where:
 - $\hat{c}_{i,\sigma}^\dagger$ and $\hat{c} _{i, \sigma}$ are the creation and annihilation operators for electrons with spin $\sigma$ at site $i$.
 - $\hat{n}_{i,\sigma}$ is the number operator for electrons with spin $\sigma$ at site $i$.
 - $t$ is the tunnelling amplitude between adjacent sites.
 - $U$ is the interaction term between two electrons occupying the same site.
 - $v_{i}$ is the on-site potential energy at site $i$

We will refer to the tunnelling term in the Hamiltonian as $\hat{T}$, the interaction potential term as $\hat{W}$, and the onsite potential term, $\hat{v}$

### The Levy-Lieb Functional

Now, we may introduce the Levy-Lieb density functional:
```math
F_{LL}[n] \equiv \inf \{ \langle \Psi | \hat{T} + \hat{W} | \Psi \rangle | \ \Psi \rightsquigarrow n, \ \Psi \in \mathcal{W}_N \}, \quad n \in \mathcal{I}_{N}
```
$\mathcal{I}_{N}$ refers to the set of allowable densities, and $\mathcal{W}_N$ refers to the set of allowable wavefunctions, described by:

```math
\mathcal{I}_N \equiv \{ n|n \geq 0, \ \nabla n^{1/ 2} \in \mathbf{L}^2, \ \int dx \ n = N \}
```

```math
\mathcal{W}_N \equiv \left\{ \Psi | \Psi \ anti(symmetric), \ \ \langle \Psi | \Psi \rangle = 1, \ \ \langle \nabla_{i} \Psi | \nabla_{i} \Psi \rangle < \inf, \ \ \text{for } i = 1,...,N \right\}    
```
We will, therefore, calculate $F_{LL}(n)$ by minimising the expectation:
```math
\langle \Psi(t) | \hat{T} + \hat{W} | \Psi(t) \rangle
```
where the parametrised state, $|\Psi(t) \rangle$, is determined by the ansatz: $|\Psi(t) \rangle = U(t) |\Psi_{i} \rangle$. The ansatz confines the solution to the desired properties of our solution. e.g. the number of particles, the number of spin orbitals, whether or not spin is conserved, etc. It does not however confine the solution to the correct density mapping, $\Psi \rightsquigarrow n$. Therefore, the minimisation procedure is constrained such that:
```math
\|d\| \equiv \| \langle \Psi(t) | \hat{D} | \Psi(t) \rangle \|  = 0
```
where  
```math
\mathbf{\hat{D}} \equiv \left[ \sum_{\sigma} \hat{c}^\dagger_{i\sigma} \hat{c}_{i\sigma} - n_i, \quad i = 1..M \right]
```
This process also yields the Levy-Lieb embedding, $\Psi[n]$.


### Density variational minimisation

The ground state energy can be calculated as:
```math
\begin{align*}
E[\hat{v}] &= \min \{\langle \Psi | \hat{T} + \hat{W} + \hat{v} | \rangle, \ \Psi \in \mathcal{W_N}  \} \\

&= \min \{ \inf\{\langle \Psi | \hat{T} + \hat{W} + \hat{v} | \rangle, \ \Psi \in \mathcal{W^{n}_{N}}  \}, \ n \in \mathcal{I_N} \} \\

&= \min \{ F_{LL}[n] +   \int{dx \ v(x)n(x)}, \ n \in \mathcal{I_N} \}
\end{align*}
```

Therefore, we can use a classical optimiser to minimise $F_{LL}(\mathbf{n}) + \mathbf{v} \cdot \mathbf{n}$. At the optimum value of $n$, we have the ground state.


### The Levy-Lieb quantum kernel

In this repository, we will also investigate the Levy-lieb kernel defined in Ref. 1 as:
```math
\kappa^{(U)}_{LL}(n_1, n_2) = \| \langle \Psi^{(U)}_{LL}[n_1] | \Psi^{(U)}_{LL}[n_2] \rangle \| ^{2}
```
We will pre-compute the Levy-Lieb kernel matrix using states $\Psi^{(U)}_{LL}$ calculated from the density constrained VQE. We will use this kernel to re-construct the Levy-Lieb functional from a select training set using classical kernel ridge regression. We will also compare the efficacy of the Levy-Lieb kernel to the classical Gaussian kernel.
```math
\kappa_G(n_1, n_2) = \frac{1}{2\sigma^2} \ e^{-\|n_1 - n_2\|^2}
```


## References
1. C. D. Pemmaraju and A. Deshmukh, “Levy-lieb embedding of density-functional theory and its quantum kernel: Illustration for the hubbard dimer using near-term quantum algorithms,” Physical Review A, vol. 106, no. 4, 2022. doi:10.1103/physreva.106.042807.

2. D. J. Carrascal, J. Ferrer, J. C. Smith, and K. Burke, “The Hubbard Dimer: A density functional case study of a many-body problem,” Journal of Physics: Condensed Matter, vol. 27, no. 39, p. 393001, Sep. 2015. doi:10.1088/0953-8984/27/39/393001 
