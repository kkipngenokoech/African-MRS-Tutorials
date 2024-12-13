# Calculating Electronic Band Structures in Quantum ESPRESSO

In this Google Colab workbook, we will use Quantum ESPRESSO to calculate and visualise the electronic band structure of silicon. As always, this is the hard version, and there is also an [easy](https://github.com/MosaicGroupCMU/African-MRS-Tutorials/blob/c157813add7469e61b875146daaa898908221c62/Google-Colab/2_QE_Silicon_Electron_Bands_Easy.ipynb) and [medium](https://github.com/MosaicGroupCMU/African-MRS-Tutorials/blob/abc5d7b730fa8fa7af5be40a0fec0e4b1af868b1/Google-Colab/QE_Silicon_Electron_Bands_Medium.ipynb) version of this workbook.

Prerequisite: `1_Quantum_Espresso_Silicon.ipynb`, Quantum ESPRESSO installed

Contributors: [Seda Oturak](https://github.com/sedaoturak), [Ismaila Dabo](https://scholar.google.com/citations?user=rN299m0AAAAJ&hl=en), [Jessica Wen](https://github.com/JessicaWen-PhD), [Cierra Chandler](https://github.com/Cierra-Chandler), [Henry Eya](https://github.com/Henrynweya)

## How to Calculate Electronic Band Structure with Quantum ESPRESSO

Quantum ESPRESSO provides us with all the tools that we need to calculate and plot the band structure. We follow the below steps:
1. Create an input file for `pw.x` and run a self-consistent field (SCF) calculation, like we did in `1_Quantum_Espresso_Silicon.ipynb`.
2. Run `pw.x` with calculation mode set to `bands`.
3. Use the `bands.x` utility to post-process the calculation data we got from step 2 into a more usable format.
4. Plot the band structure with `plotband.x`.
5. Analyse the generated band structure file to interpret the electronic properties of the material.

## How does the calculation work?

The `bands` calculation in the `pw.x` program is *non self-consistent* so we need to read out the ground state electron density, Hartree, exchange, and correlation potentials that we found in the SCF calculation.

The `pw.x` program finds the Kohn-Sham eigenfunction and eigenvalues without updating the Kohn-Sham Hamiltonian each time it iterates (hence why it is NOT a self-consistent calculation).

We need to specify the k-points at which the Eigenvalues will be calculated. A reminder that you can use the [See-K-path tool by Materials Cloud](https://www.materialscloud.org/work/tools/seekpath) to visualise the K-path. If you're advanced, you can use [XCrysDen](http://www.xcrysden.org/) to select a path in k-space.

We can specify `nbnd` so that it doesn't just do the default (which is when it calculates half the number of valence electrons, i.e. only the occupied ground state bands) - we are usually also interested in the unoccupied bands above the Fermi energy. The number of occupied bands can be found in the `scf` output as the number of Kohn-Sham states.

Explanation from the [Quantum ESPRESSO tutorial by pranabdas on GitHub](https://pranabdas.github.io/espresso/hands-on/bands/).

## Prepare Quantum ESPRESSO input file

First, we want to create a calculation folder and navigate into it. This is a good habit to get into because when we want to run lots of calculations, we want to keep them organised. It's also important to keep all the calculations and output files in the same folder so the post-processing can use the results from previous calculations.

The calculation is for a unit cell of diamond silicon. Remember that the definition of the input parameters of the `pw.x` executable can be found at `www.quantum-espresso.org/Doc/INPUT_PW.html`.

We can use the optimal cutoff energies and k-points that we found for silicon in the convergence tests during the `1_Quantum_Espresso_Silicon.ipynb` workbook.

It might also be useful to use this paper, [(Herman, 1972)](https://ieeexplore.ieee.org/document/4055356), as a reference.

In the previous workbook, we visualised the silicon crystal. Here, we'll dive straight into the self-consistent field (SCF) calculation.

## Electronic band structure calculation

Now we can run the `bands` calculation using the `pw.x` executable. To begin, we need to create a band structure calculation input file.

## Post-processing and visualisation

After doing the `bands` calculation, we can use the `bands.x` executable to do the post-processing of the data. `bands.x` also requires its own input file.

Visualise the calculated band structure with `plotband.x`, either by running it interactively through typing `plotband.x` in your terminal or by providing an input file.

We will first use `plotband.x`. Here's a hint for what you should put in the terminal prompts.


```
     Input file > si_bands.dat
Reading    8 bands at     91 k-points
Range:   -6.4340   12.6300eV  Emin, Emax, [firstk, lastk] > -6, 16
high-symmetry point:  0.5000-0.5000 0.5000   x coordinate   0.0000
high-symmetry point:  0.0000 0.0000 0.0000   x coordinate   0.8660
high-symmetry point:  0.0000-1.0000 0.0000   x coordinate   1.8660
high-symmetry point:  0.2500-1.0000 0.2500   x coordinate   2.2196
high-symmetry point:  0.0000 0.0000 0.0000   x coordinate   3.2802
output file (gnuplot/xmgr) > si_bands.gnuplot
bands in gnuplot/xmgr format written to file si_bands.gnuplot                                                                                                                                                                                                                                                
output file (ps) > si_bands.ps
Efermi > 6.6416
deltaE, reference E (for tics) 4, 0
bands in PostScript format written to file si_bands.ps   
```

We can now try an alternative way with matplotlib and numpy.

Most of the processing is converting the si_bands.dat.gnu data into x-y data that we can plot with any software. The data in si_bands.dat.gnu is formatted relatively predictably, so we can go band-by-band and extract the data into energy vs k-point data.

This process stores the lowest band energy first, then the next lowest, and so on until all the bands in the band structure have been extracted.

Explanation from [Levi Lentz's Band Diagram Tutorial for Quantum Espresso](https://blog.levilentz.com/band-diagram-tutorial-for-quantum-espresso/).

The high-symmetry points like those labelled on our diagram (Γ, X, U, L) can be found in the post-processing output file (`pp.si.bands.out`).

We can use the plot to determine the band gap value, but we can be more accurate by finding the difference between the highest occupied level and the lowest unoccupied level from the scf calculation output. Compare this to the experimental value of the silicon band gap of 1.170 eV [(Bludau, Onton and Heinke, 1974)](https://pubs.aip.org/aip/jap/article-abstract/45/4/1846/507373/Temperature-dependence-of-the-band-gap-of-silicon).

The Fermi energy is halfway between these levels.

## Drawbacks of this method to calculate band gaps

In these calculations, LDA or GGA are commonly used as the exchange-correction functionals. However, these severely underestimate band gaps (i.e. experimental band gap values are generally much higher). According to [pranabdas](https://pranabdas.github.io/espresso/hands-on/bands/), this difference can be attributed to:

1. Approximations used in the exchange correction functional, and
2. A derivative discontinuity term, originating from the density functional being discontinuous with the total number of electrons in the system.

The second point is the larger contributor to the error. Many different techniques have been developed that partially address this problem, such as the GW approximation, which you can specify in the input file.

"Strategies to improve band gap prediction at moderate to low computational cost now been developed by several groups, including [Chan and Ceder](https://doi.org/10.1103/PhysRevLett.105.196403) (delta-sol), [Heyd et al.](https://doi.org/10.1063/1.2085170) (hybrid functionals), and [Setyawan et al.](https://doi.org/10.1021/co200012w) (empirical fits)." -- see the [Quantum ESPRESSO tutorial by pranabdas on GitHub](https://pranabdas.github.io/espresso/hands-on/bands/).
