---
layout: post
title: Principles and general usage of Ab initio quantum chemistry methods
categories: [Research]
---

I will begin assuming the reader knows some basic principles of quantum mechanics and the importance of the Schrödinger equation. While this will surely help in grasping some of the concepts easily, it is not a hard imposition. I will try my best to keep the description as simple and practical as possible so that on completion you don't stay empty handed. 

Schrödinger equation is believed to be the ultimate equation that governs the atomic world. It may apparently look gory to a first time visitor of quantum mechanics. To them, I refer the [wikipedia page](https://en.wikipedia.org/wiki/Schr%C3%B6dinger_equation) for its formal introduction. This partial differential equation is really impossible to solve analytically beyond a handful of cases. Numerical solutions are possible, but again with increasing number of particles, this goes out of hand. Even employing all the present day state of the art supercomputers together, numerical solution becomes infeasible beyond a certain limit.

So to achieve some insight into the atomic world of molecules many approximate techniques were developed over the years, pushing the limit of solving this Schrödinger equation to a level of satisfactory accuracy. With time scientists have kept on improving previous methods with ingenious mathematical construction which respects the underlying physics . Some of these methods are computationally expensive and some are relatively cheaper. The extra computational cost of a method is associated with the accuracy of the method, so depending on  purpose, researchers select the method to employ. Computational power is not free, and an unnecessary accurate solution is a waste of time and resource. Therefore in here, I will try to summarize the different methods that are widely used in the community of computational chemistry and specify their general use. 

These methods can be broadly divided into two categories: 

1. Wavefunction based approach and 
2. Density functional based approach.

 **Wavefunction based approach**

In these methods, the solution of the Schrödinger equation is obtained as a sum of Slater determinants. The orbitals and their coefficients are optimised using various numerical techniques until their convergence. The major drawback of this approach comes from the complete neglect of electron correlations. To ameliorate this, improvisations were done that lead to correlated wavefunction based approaches. An example of simple wavefunction based approach is Hartre-Fock theory, and examples of correlated wavefunction based approaches imclude second-order Moller– Plesset perturbation theory (MP2), Coupled cluster with single, double and triple perturbative excitations (CCSD(T)), and Complete active space with second-order perturbation theory (CASPT2) - a multireference perturbation method. 

1. CCSD(T)

   - This is a gold standard of quantum chemistry. 

   - Scaling with number of electron = N<sup>7</sup>

   - Accuracy ~ 1 kcal/mol

2. MP2

   - Scaling with number of electron = N<sup>5</sup>

   - Performance reduces during making and breaking electron pairs

   - Extremely good for nonbonded interactions and internal conformational energetics where this method gives a very high accuracy of 0.3 kcal/mol 

3. CASPT2

   - For effective implementation, this method requires careful definition of the active space of electrons to be treated at the multireference level

   - Scaling with number of electron = e<sup>N</sup> 

   - Only to be used when MP2 and CCSD(T) methods fails and/or one needs to incorporate multireference of electrons in active space

   

**Density functional based approach**

In this kind, the system's energy is expressed as a functional of electron density. Density functional theory (DFT) follows from Hohenberg–Kohn formalism. The electron density is a spatially varying quantity which depends on only 3 coordinates as opposed to the 3N coordinates of N electrons . Hence, the computational effort to solve DFT equations is comparable with that of the basic Hartree–Fock. This makes DFT easy to implement computationally. DFT also has its limitation that the exact functional of energy is unknown, and it has to be constructed by suitable approximations such as studying the behaviour of the electron gas. Major improvement has been achieved in the past years in figuring out the functional form such that it generates results to a very good accuracy. 

Two principal classes of widely used functionals are:

1. gradient-corrected

   - Local-density functionals + gradient of the electron density.

   - Example: BLYP
     - average error of 7.09 kcal/mol for G2 data set

2. hybrid functionals. 

   - Local-density functionals + gradient of the electron density + empirically fitted admixture of exact Hartree-Fock exchange. 

   - Example: B3LYP

     - average error of 3.11 kcal/mol for G2 data set

     - average errors for bond energies of small transition metal complexes in the range of 3–5 kcal/mol

Till now, dispersion (van der Waals) interaction is not established in DFT.

3. Time-dependent version of DFT (TDDFT) 

   Considerably less expensive than other excited-state methods such as CCSD(T) and CASPT2. 



I think most will agree that the above methods are the most widely used ab initio techniques found in research articles. However, many other methods and variants of the above methods do exists. My intension here was to give a rough idea of the most popular ones along with some intuition from the knowledge of cost and accuracy associated with each of them. 

Considering the above methods, in most cases (NOT ALL) we can grade them reasonably as:

 CASPT2 > CCSD(T) > TDDFT > MP2 > B3LYP > BLYP > Hartree–Fock

Going towards the right, you loose accuracy by saving computation time. Baring few cases where methods to the left are indispensable, in most cases a very high accuracy does not worth the computation and need. Thus, I will finish with the general usage from my understanding which I think one can take as a rule of thumb. 

To study small molecules, one can use CCSD(T). But before beginning CCSD(T), performing a geometry optimization and basis set convergence test with a less expensive method can further boost the overall process.

To study reactive chemistry in medium to large systems, DFT is at present the best approach. 

To study nonbonded interactions and internal conformational energetics in both small and large systems MP2 methods are generally used. Systems containing a few hundred atoms are treated routinely with MP2. This helps in the development in molecular force-fields. 



- Friesner, Richard A. "Ab initio quantum chemistry: Methodology and applications." *Proceedings of the National Academy of Sciences* 102.19 (2005): 6648-6653.

- Hafner, J., Wolverton, C., & Ceder, G. (2006). Toward Computational Materials Design: The Impact of Density Functional Theory on Materials Research. *MRS Bulletin,* *31*(9), 659-668. doi:10.1557/mrs2006.174