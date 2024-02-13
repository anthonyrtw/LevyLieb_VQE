# Variational Quantum Eigensolvers - The Levy-Lieb Procedure

This repository contains code for calculating the Levy-Lieb density functional, $F_{LL}(\mathbf{n})$ of a Hubbard dimer using a density-constrained Variational Quantum Eigensolver (VQE) and subsequently utilizing $F_{LL}(\mathbf{n})$ in the calculation of the ground energy. This work follows closely the theory and implementation outlined in Ref. 1.

## Packages Used
 - qiskit
 - qiskit_nature
 - qiskit_algorithms
 - numpy
 - matplotlib
 - tqdm

## Theoretical Background

### The Fermi Hubbard Model

The Fermi-Hubbard Model is a lattice model in which electrons experience an interaction potential, on-site potential and can tunnel between lattice sites. Each site has two spin orbitals, corresponding to up and down. Therefore each site can possess a maximum of two electrons. In this project, we consider the two site Fermi-Hubbard lattice whose hamiltonian is as follows:

```math
\begin{equation}
\hat{H} = -t \sum_{\sigma=\uparrow,\downarrow} (c^\dagger_{1\sigma} c_{2\sigma} + \text{h.c.}) + U \sum_{i} \hat{n}_{i\uparrow} \hat{n}_{i\downarrow} + \sum_{i} v_i (\hat{n}_{i\uparrow} + \hat{n}_{i\downarrow}) 
\end{equation}
```
Where:
 - $\hat{c}_{i,\sigma}^\dagger$ and $\hat{c} _{i, \sigma}$ are the creation and annihilation operators for electrons with spin $\sigma$ at site $i$.
 - $\hat{n}_{i,\sigma}$ is the number operator for electrons with spin $\sigma$ at site $i$.
 - $t$ is the tunnelling amplitude between adjacent sites.
 - $U$ is the interaction term between two electrons occupying the same site.
 - $v_{i}$ is the on-site potential energy at site $i$

We will refer the tunnelling term in the Hamiltonian, $\hat{T}$, the interaction potential term, $\hat{W}$, and the onsite potential term, $\hat{v}$

### The Levy-Lieb Functional

Now, we may introduce the Levy-lieb density functional:
```math
\begin{equation}
    F_{LL}[n] \equiv \inf \left\{ \langle \Psi | \hat{T} + \hat{W} | \Psi \rangle \, \middle| \, \Psi \rightsquigarrow n, \Psi \in \mathcal{W}_N \right\}, \quad n \in \mathcal{I}_{N}
\end{equation}
```
$\mathcal{I}_{N}$ refers to the set of allowable densities, and $\mathcal{W}_N$ refers to the set of allowable wavefunctions, described by:

```math
\begin{equation}
\mathcal{I}_N \equiv \left\{ n \, | \, n \geq 0, \nabla n^{1/ 2} \in \mathcal{L}^2, \int dx \, n = N \right\}\end{equation}
```

```math
\begin{equation}
\mathcal{W}_N \equiv \left\{ \Psi \, | \, \Psi \ anti(symmetric), \ \langle \Psi | \Psi \rangle = 1, \ \langle \nabla_{i} \Psi | \nabla_{i} \Psi \rangle < \inf, \ \text{for } i = 1,...,N \right\}    
\end{equation}
```
We will therefore calculate $F_{LL}(n)$ by minimising the expectation:
```math
\begin{equation}
\langle \Psi(t) \ | \ \hat{T} + \hat{W} \ | \ \Psi(t) \rangle
\end{equation}
```
where the parametrised state is determined by the ansatz: $|\Psi(t) \rangle = U(t) |\Psi_{i} \rangle$. The ansatz confines the solution to the correct number of particles, whether or not spin is conserved, etc. It does not however confine the solution to the correct density mapping, $\Psi \rightsquigarrow n$. Therefore, the minimisation procedure is constrained such that:
Subject to the constraint 
```math
\begin{equation}
\|d\| \equiv \| \langle \Psi(t) | \hat{D} | \Psi(t) \rangle\ \|  = 0
\end{equation}
```
where  
```math
\begin{equation}
\mathbf{\hat{D}} \equiv \left[ \sum_{\sigma} \hat{c}^\dagger_{i\sigma} \hat{c}_{i\sigma} - n_i, \quad i = 1..M \right]
\end{equation}
```

### Density variational minimisation

The energy can be calculated as:
```math
\begin{equation}
E[\mathbf{v}, \mathbf{n}] = F_{LL}[\mathbf{n}] + \mathbf{v} \cdot \mathbf{n}
\end{equation}
```
Therefore, by varying the density, $n$ such that $0 < n_i < 2$ and $n_1 + n_2 = 2$, we can calculate the minimum ground state of the Hubbard dimer.


## References
1. C. D. Pemmaraju and A. Deshmukh, “Levy-lieb embedding of density-functional theory and its quantum kernel: Illustration for the hubbard dimer using near-term quantum algorithms,” Physical Review A, vol. 106, no. 4, 2022. doi:10.1103/physreva.106.042807.

2. D. J. Carrascal, J. Ferrer, J. C. Smith, and K. Burke, “The Hubbard Dimer: A density functional case study of a many-body problem,” Journal of Physics: Condensed Matter, vol. 27, no. 39, p. 393001, Sep. 2015. doi:10.1088/0953-8984/27/39/393001 
