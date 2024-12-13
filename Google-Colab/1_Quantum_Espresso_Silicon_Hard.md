# Introduction to electronic structure optimization of silicon using Quantum ESPRESSO

Contributors: [Seda Oturak](https://github.com/sedaoturak), [Ismaila Dabo](https://scholar.google.com/citations?user=rN299m0AAAAJ&hl=en), [Jessica Wen](https://github.com/JessicaWen-PhD), [Cierra Chandler](https://github.com/Cierra-Chandler), [Henry Eya](https://github.com/Henrynweya)

Once you've compiled Quantum ESPRESSO on your computer and installed ASE, numpy, matplotlib, libfftw3-3, libfftw3-dev and libfftw3-doc, we can get started with calculations!

### Structure of the class

1.   Introduction to Quantum Mechanics
2.   Schrödinger Equation
3.   Density-Functional Theory (DFT)
4.   Application of DFT to Single-Particle Calculations
5.   Hands-on Workshop

### How to use these workbooks

*   Work in pairs and help others!
*   Fill in the blanks and run code blocks to see if it works.
*   Answers will be shared after the class.
*   This hard level workbook is designed for people have dabbled in some QuantumESPRESSO before (where you can run QuantumESPRESSO on your own computer).
*   If this hard level is too challenging for you, you can switch to the [intermediate difficulty Google Colab workbook](https://github.com/MosaicGroupCMU/African-MRS-Tutorials/blob/7c1881ffde25cff269037db84b8ff652ed7bdb4c/Google-Colab/1_Quantum_Espresso_Silicon_Medium.ipynb), which is designed with some more experience in Python and using the terminal, or the [easy difficulty Google Colab](https://github.com/MosaicGroupCMU/African-MRS-Tutorials/blob/864cb5a0c0cd1da4855178ef804bbf38b7e5ce3b/Google-Colab/1_Quantum_Espresso_Silicon_Easy.ipynb) if you are unfamiliar with Python and terminal commands.  

# Prepare Quantum ESPRESSO input file

This is where we start setting up the data and information for our QuantumESPRESSO calculations.

We first need to start with the Self-Consistent Field (SCF) calculations. The parameters in the QuantumESPRESSO input file requires some playing around to achieve the best trade-off between accuracy and run-time.

The calculation is for a unit cell of diamond silicon. The definition of the input parameters of the `pw.x` executable can be found at `www.quantum-espresso.org/Doc/INPUT_PW.html`.

You first need to create a calculation folder and navigate into it. All following calculation files and outputs must be added to this folder, because post-processing executables will look in this folder to find the outputs we calculated before.

Refer to Dr Nelson Dzade's Overview of QE slides and `www.quantum-espresso.org/Doc/INPUT_PW.html` for the format and what to include in the input file.

You can use ASE tools to extract information from Quantum ESPRESSO input and visualize the crystal with the x3d viewer. Remember to create a supercell of multiple unit cells, otherwise you'll only see the two atoms of silicon that make up one unit cell!

HINT: if you're getting an AttributeError where the 'NoneType' object has no attribute 'append', it's probably because you're not quite formatting your input file correctly. For example, you might be missing commas.

# Run Quantum ESPRESSO using the input file

Make sure that the pseudopotential file is present in the working directory. It's time to start the self-consistent field calculation, which is often the longest part of any calculations you do. This should produce the potential energy.

### What is the Self-Consistent Field (SCF) calculation?

The SCF calculation is an iterative computational method used to solve the many-body Schrödinger equation for electrons in atoms and molecules.

It starts with an initial guess for the electron distribution and iteratively refines it until convergence is reached. Each electron is treated as if it moves in the average field created by all other electrons, simplifying the complex many-body problem (mean-field approximation).

The calculation aims to minimise the total energy of the system with respect to the electron density, thus solving for molecular orbitals and their energies by using (in this case) plane wave basis sets to represent the electron wavefunctions.

The iteration stops when the change in energy or electron density between successive steps become sufficiently small (given by the convergence threshold).

### What are the pseudopotential files used for?

The pseudopotential files replace the full electron system with an effective potential that accounts for the core electrons, so that we don't need to calculate where every single electron in the system goes. This reduces computational complexity. Well-designed pseudopotentials aim to balance computational efficiency with accuracy across different chemical environments.

You can read more about choosing pseudopotentials on the [VASP wiki](https://www.vasp.at/wiki/index.php/Choosing_pseudopotentials).

### How do you do this practically?

You can run the pw.x executable in the terminal using the input file to create the output file. It may help to print the content of the output file.

You can then extract the energy from the output file, which is given in Rydberg constants.

# Convergence test with respect to the cutoff energy

Convergence tests are an important part of any DFT calculation. We do convergence tests for the cutoff energy and the k-point sampling.

You can read more about convergence tests in this [Choudhary and Tavazza, 2020](https://pmc.ncbi.nlm.nih.gov/articles/PMC7066999/) paper.

### About Cutoff Energy

The wavefunctions of electrons we use in these DFT calculations are Kohn-Sham wavefunctions, and we can theoretically create these wavefunctions by summing an infinite number of plane waves.

However, we are not able to compute an infinite number of plane waves, so we use the cutoff energy to specify the maximum kinetic energy of the plane waves used (i.e. we truncate the infinite expansion of the Kohn-Sham wavefunction into plane waves, thus ignoring all plane waves with energy above this threshold). Setting a higher cutoff energy increases the size of the basis set (plane waves) used to represent electronic wavefunctions, and controls the number of plane waves included in the calculation.

Higher cutoff energies generally lead to more accurate results (because you are using more plane waves) but also increases computational time. For most systems, a cutoff of 25-40 Ry (340-544 eV) may be sufficient, but this can vary widely.

### About Convergence Tests

Convergence tests achieve several goals:

1.   Accuracy: they make sure that the properties we calculated are sufficiently accurate and not affected by numerical artifacts.
2.   Property convergence: we want to find the point at which the properties we care about (e.g. total energy, bond lengths, electronic structure, etc.) are stabilised with increasing cutoff energy or k-point density.
3.   System-specific optimisation: we can account for the unique characteristics of different materials, such as band gap, crystal structure, and chemical composition.
4.   Validation of results: convergence tests provide confidence in the calculated results by demonstrating that further increases in cutoff energy or k-point density don't significantly change the outcomes.

Convergence tests allow us to establish a balance between accuracy and computational cost, which ensures that our DFT calculations can produce reliable and reproducible results for the properties we are investigating.

You can read more about convergence tests on [HJK Group at MIT's Convergence 101 post](https://hjkgrp.mit.edu/tutorials/2012-04-17-convergence-101/).

### How do you do this practically?

Create a list for cutoff energies to be tested, and then iteratively calculate the energy after replacing the cutoff energy value in the input file.

You can then plot the convergence graph of cutoff energies (i.e. the final energy of the structure for each cutoff energy tried).

We want to find a cutoff energy where the total energy just starts to level off.

# Convergence test with respect to the k-point sampling

The k-point sampling density specifies how many points in the [Brillouin zone](https://eng.libretexts.org/Bookshelves/Materials_Science/Supplemental_Modules_(Materials_Science)/Electronic_Properties/Brillouin_Zones) are used to calculate the property we are investigating (essentially by providing an efficient means of integrating periodic functions of the wave vector in the Brillouin zone).

This is usually done by taking a grid of k-points, often generated by methods like [Monkhorst-Pack sampling](https://journals.aps.org/prb/abstract/10.1103/PhysRevB.13.5188)

From [Choudhary and Tavazza, 2020](https://pmc.ncbi.nlm.nih.gov/articles/PMC7066999/):

The total energy of the system is the most important output of a DFT calculation, and it is obtained by numerically integrating the Hamiltonian over the Brillouin zone. The k-points are a generic way to discretize such as integral. The quality of the results heavily depends on the number of these points on the mesh-grid as well as the method generating the mesh-grid itself used in such integration. The number of points can be arbitrarily increased to increase the precision of calculations. However, the higher the number of irreducible k-points, the higher the computational cost. Therefore, finding the optimum number of k-points to determine the total energy within a specified tolerance (i.e. “converging” on the k-point mesh) is extremely important.

### How do you do this practically?

Again, we create a list of the k-points to be tested, and iteratively calculate the energy after replacing the k-points in the input file. Remember to use the optimal cutoff energy value we calculated from the previous step!

Plot the convergence graph to see what density of k-points provides us with a relatively accurate energy value.

# Lattice parameter

We can now use an appropriate cutoff energy and k-point sampling found from the convergence tests to find the energetically favourable lattice parameter by iterating the self-consistent field calculation over many lattice parameters.

### How do you do this practically?

Again, we initialise an input file but this time with the optimum cutoff energy value and k-point density. We also create a list of lattice constants to be tested, and iteratively calculate the energy of the structure for each lattice parameter.

We can then plot the potential energy curve for different lattice parameters to find the optimal lattice parameter.
