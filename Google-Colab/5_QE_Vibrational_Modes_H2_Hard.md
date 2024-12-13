# Vibrational modes of molecules

In this tutorial, you will learn how to calculate the vibrational frequencies of molecules from first principles, using molecular hydrogen as an example.

Contributors: [Lorenzo Bastonero](https://github.com/bastonero), [Jessica Wen](https://github.com/JessicaWen-PhD)

Pre-requisites: `1_Quantum_Espresso_Silicon.ipynb`, downloaded Quantum ESPRESSO

As always, there is an [easy](https://github.com/MosaicGroupCMU/African-MRS-Tutorials/blob/772f86d662fa5c5be12784931acd3a98cc121ca9/Google-Colab/5_QE_Vibrational_Modes_H2_Easy.ipynb) and a [medium](https://github.com/MosaicGroupCMU/African-MRS-Tutorials/blob/772f86d662fa5c5be12784931acd3a98cc121ca9/Google-Colab/5_QE_Vibrational_Modes_H2_Medium.ipynb) version of this workbook, which walk you through the code in Google Colab. This workbook is designed for people who have dabbled in some Quantum ESPRESSO before.

# The harmonic approximation

Atoms in molecules and solids vibrate around their _equilibrium_ positions.

![n2harmonicoscillation.png](https://upload.wikimedia.org/wikipedia/commons/a/aa/N2ground.png)

## $H_2$ vibrational mode: finite differences of forces

Make sure to download the hydrogen pseudopotential from this repository!

### Step 1: find the _equilibrium_ geometry.

Fill out the base input file (remember definitions can be found [here](https://www.ecosia.org/search?q=quantum+espresso+input+file&addon=chrome&addonversion=6.0.4&method=topbar&addon=opensearch)). Try using the Marzari-Vanderbilt-DeVita-Payne cold smearing (see [PRL 82, 3296 (1999)](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.82.3296)).

Calculate the potential energy surface for different distances of the atoms. We use a similar process to the exercise `1_Quantum_Espresso_Silicon.ipynb`.

Plot the energy variation over different atomic distances.

Perform a `relax` calculation using `pw.x`. First, we create another input file. Since we are relaxing the structure, we need to allow the ions (the core of the hydrogen atoms) to move.

There are different algorithms that allow this to occur - we will use the BFGS algorithm. You can read more about the BFGS algorithm [here](https://paulino.princeton.edu/courses/cee8813H/2020/class_notes/4_9_20/Online4_2_Newton%20&%20Quasi-Newton_Algorithms.pdf).

Now run the `relax` calculation with `pw.x` to produce h2.relax.out.

- How do these results compare to the potential energy surface?
- Does it change the result if you start from a different point?
- When does it apply?

### Step 2: compute forces on displaced atoms.

For $H_2$ we only need to displace one atom in the direction along the bond, since this diatomic molecule has only this degree of freedom.

Something to think about: what about the $H_2O$ molecule instead? (We will explore this in the `6_QE_Vibrational_Modes_H2O` exercise).

Replace the atomic positions with the desired distance for which we want to find the forces.

### Step 3: calculate the _force constant_

The force constant _C_ of a material is defined as the second-order derivative with respect to atomic positions of the total energy.

For the $H_2$ molecule, this means:
$
C = \frac{\partial^2 E}{\partial d^2}
= - \frac{\partial F}{\partial d}
= - \lim_{\delta d \rightarrow 0} \frac{F(d_0+\delta d) -F(d_0)}{\delta d}
\approx - \frac{F(d_0+\Delta d) -F(d_0)}{\Delta d}
\approx - \frac{F(d_0+\Delta d)}{\Delta d}
$

### Step 4: calculate the vibrational _frequency_

In the classic harmonic model, atoms are subjected to the dynamics described by: $F = m \ddot{d} = - C~d$

The time solution is: $d(t) = A \sin(\omega t + \phi)$, where $\omega = \sqrt{\frac{C}{m}}$.

Let's calculate the oscillation frequency $\omega$ for the $H_2$ molecule!

We can convert the angold frequency units to THz

## $H_2$ vibrational mode: density-functional perturbation theory

### Step 1: compute the ground state using DFT

This step should also account for the structural optimization, meaning forces on all atoms and stress tensor should be close to zero.

A DFT calculation is then needed to provide the ground-state charge density for the DFPT calculation.

### Step 2: compute the _dynamical matrix_ via DFPT

Run the density-functional perturbation theory calculation to compute the dynamical matrix.

Define the inputs using plain string text and write to `h2.ph.in`.

Run the process using the `ph.x` executable to produce h2.ph.out.

Read the results using ASE.

How does it compare with the value computed via finite differences?

Why are they different?

How does it compare with the experimental value 4161 $\mathrm{cm}^{-1}$?
(see _B. P. Stoicheff. 1957. HIGH RESOLUTION RAMAN SPECTROSCOPY OF GASES: IX. SPECTRA OF H2, HD, AND D2. Canadian Journal of Physics. 35(6): 730-741. https://doi.org/10.1139/p57-079_)

### Solution to discrepancy of finite differences and DFPT

The issue is that we did not use the correct formula! In fact, the calculation of frequencies involves a diagonalization of a (3N,3N) matrix. Look at the effect by diagonalizing the correct matrix, and then try to explain what you should modify in the previous section and why.

Calculate the eigenvalues of the matrix using Numpy.

How does it compare with the force constants previously computed?

What are the other eigenvalues corresponding to?

Why are they zero?
