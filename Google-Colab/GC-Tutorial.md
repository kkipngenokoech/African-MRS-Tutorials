# Google Colab Tutorial

## What is Google Colab and why use it?

[AiiDA](https://www.aiida.net/index.html) is an open-source Python infrastructure to help researchers with automating, managing, persisting, sharing and reproducing the complex workflows associated with modern computational science and all associated data. It is very helpful framework for high throughput calculations.


## Quantum ESPRESSO
Aiida has many plugins for popular softwares including [VASP](https://aiidateam.github.io/aiida-registry/aiida-vasp), [Gaussian](https://aiidateam.github.io/aiida-registry/aiida-gaussian), and [Wannier90](https://aiidateam.github.io/aiida-registry/aiida-wannier90). There is currently a plugin for [Quantum Espresso](https://aiida-quantumespresso.readthedocs.io/en/latest/) that integrates the Quantum ESPRESSO software suite. Compute a variety of material properties with the popular open source DFT code with automatic [data provenance](https://www.nnlm.gov/guides/data-glossary/data-provenance#:~:text=Definition,to%20where%20it%20is%20presently) provided by AiiDA. 

# Tutorial 

1. Open google.colab.com 
   
`Conda create -n aiida python==3.11`

2. Upload the python notebook to google colab. 

`Conda activate aiida`

3. Um run this line?
```# clone Quantun ESPRESSO git file
!git clone https://github.com/QEF/q-e.git

# install dependencies
!apt-get install -y libfftw3-3 libfftw3-dev libfftw3-doc

# run configuration and installation
%cd q-e
!DFLAGS='-D__FFTW3 ' FFT_LIBS='-lfftw3' ./configure
!make pw ```

