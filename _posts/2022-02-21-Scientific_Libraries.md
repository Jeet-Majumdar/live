---
layout: post
title: Scientific Libraries 
categories: [Research, Tech]
---

**BLAS**

- Basic dense matrix operations

  - Multiplication, Addition, Rank update, Triangular Solve

- Divided into 3 levels

  | Level | Type          | Example            | # mem refs |
  | ----- | ------------- | ------------------ | ---------- |
  | 1     | Vec-Vec       | DAXPY (y=x+y)      | 3n         |
  | 2     | Matrix-Vec    | DGEMV (y = Ax + y) | n**2       |
  | 3     | Matrix-Matrix | DGMM (C = AB+C)    | 4n**2      |

- Try to use highest level calls 



**LAPACK**

- Matrix Solvers
  - Simultaneous linear equations, least-squares solutions, eigenvalue problems
  - Matrix factorizations (LU, Cholesky, QR, SVD, Schur)
- Relies heavily on BLAS for computation



**ScaLAPACK**

- BLACS
  - Message passing interface for linear algebra
  - 2D arrays on 2D processes grids
  - Generation of process grids
  - Communication of matrices
- Parallel LAPACK and BLAS routines (through PBLAS)
- Extends the BLAS / LAPACK interface 
  - Addition of P prefix into the routines 
  - Example: PDGEMM



**Interesting Implementations**

- PLASMA
  - Manycore optimized linear algebra routines
  - Potential successor of LAPACK
- MAGMA and CULA
  - LAPACK0type routines for GPUs



**Sparse Matrices**

- PDEs, network simulation
- Only store non-zero elements
- Density and sparsity pattern affects performance
  - No single general purpose solution
  - Many solvers available
- Hypre
- KLU, Paraklete, AztecOO
- SuperLU, UMFPACK, MUMPS
- CuSP. CuSPARSE
  - Solvers for NVIDIA GPUs



**FFT Libraries**

- FFTW (Fast Fourier Transform in the West)

  - 1D, 2D and 3D complex and real FFTs
  - Two versions commonly use
    - Version 2: MPI support
    - Version 3: Much better performance, MPI support in development
  - Planning has multiple levels of comprehensiveness

  - ESTIMATE, MEASURE, PATIENT, EXHAUSTIVE
  - Plans can be saved in a "wisdom file" and reused 

- Cray CRAFFT

  - Dynamically selects the best FFT routine 
  - Serial and parallel routine
  - See info_crafft

- AMD ACML
- Intel MKL and IPP
- CuFFT
  - FFT routines for Nvidia GPUs



**Domain Specific Libraries**

- OpenMM
  - Molecular Dynamics
  - Supports GPUs
- Libint
  - Two-body molecular integrals
- Overture
  - PDE solvers designed for CFD
- Libgenometools
  - Genome analysis functions
