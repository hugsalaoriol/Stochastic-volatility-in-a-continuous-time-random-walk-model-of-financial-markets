# Stochastic volatility in a continuous-time random walk model of financial markets

Code accompanying the work:
> **Stochastic volatility in a continuous-time random walk model of financial markets,**
> H. Sala, J. Perelló;
> Physics degree final thesis,
> 2026

This repository reproduces the results and the figures of the work, in which the Montroll-Weiss continuous-time random walk (CTRW) formalism is extended through Markovian stochastic volatility (SV) transitions. Beyond the results and figures shown in the work, the code is structured in such a way that can be easily adapted to any other ansatz for the volatility kernel and jump and waiting times distributions (see section II.C). This new kernel and distributions can be fitted in functions and the propagator, tails and second-order moments will be recomputed automatically with the surrounding machinery left untouched.

--- 
## Overview

In the CTRW framework, returns accumulate through a succession of jumps separated by random waiting times. In the classical formulation the volatility is intrinsic to the jump distribution and held constant. Extending the formalism with Markovian stochastic-volatility transitions makes the jump distribution depend on an underlying process that is renewed at each step, with the volatility entering a given jump forced to depend on its
value at the previous one. Coupling the Montroll–Weiss framework to SV in this way yields:
 
- power-law tails in the return distribution;
- A superdiffusive character of the second-order moment, until the relaxation timescale is reached, where the normal diffusion is recovered.

---
## Repository structure

```
.
├── README.md
├── requirements.txt                 
├── transition_kernel_pdf.ipynb                 
└── SV_CTRW.ipynb
    
```

### Code 1: tranition_kernel_pdf.ipynb

Estimates the model parameters by OLS from empirical series (Dow Jones Industrial Average index, `^DJI`, 2024-2025), and computes the volatility transition pdf for the OU, expOU, Heston, ARCH and GARCH processes. This pdf are plotted considering previous step volatility and are compared to the actual volatility for that step from the data (this is repeated every 10 jump steps). Beyond this graphical comparison, the agreement is quantified with RMSE and log-likelihood estimators, assessing the viability of each kernel (positivity, normalization, relaxation, etc.) before being fed into the CTRW workframe.

 
Produces: Fig. 5-10.

### Code 2: SV_CTRW.ipynb

Computes the return propagator `P(γx,λt|σ₀)/γ` from the Montroll–Weiss solution, the tail profile, and the second moment `⟨X²(λt)|σ₀⟩`. It is organised in two parts:

The **first part** (cells 2–7) is dedicated to the analysis of the results: it plots the propagator in dimensionless units, fits the tail profile by OLS over a sliding window of points, and computes the second moment, fitting two normal-diffusion regimes and one superdiffusive regime. This part relies on a set of helper functions defined in cell 2.

The **second part** reuses the same machinery to generate the figures presented in the paper.
 
Parameters can be computed as in Code 1 (estimated from the `^DJI` series) or set to values drawn from the literature (see the references cited inline in the code).
 
Produces: Fig. 2-4, 11, 12.

---
## Requirements
 Built with Python 3.12.12. Install the dependencies with:
 ```bash
pip install -r requirements.txt
```
Core dependencies:
- `numpy`, `scipy`, `math` — numerical routines and transforms
- `matplotlib`, `mpl_toolkits` — figures
- `yfinance`,`pandas` — data source and handling
- `mpmath` — high-precision Talbot inverse Laplace transform
- `statsmodels` — regressions (OLS)
- `arch` — GARCH-family benchmarks

---
## Data

The model is calibrated to the **Dow Jones Industrial Average index `^DJI`** daily series over the period **2024-2025**, source: ** Yahoo Finance**.

---
## Usage


1. Run the kernel viability checks:
   ```bash
   python tranition_kernel_pdf.ipynb
   ```
2. Run the main analysis (propagator, tails, second moment):
   ```bash
   python SV_CTRW.ipynb
   ```

### Configurable options
 
The pipeline is not hard-wired to a single setup: its main building blocks are exposed as variables that can be swapped to adapt the model, this lets the same machinery be applied to other ansatzs beyond the ones in the paper. The key ones are (cell 3):
 
- `model_f` — the volatility transition kernel (the SV process: OU / expOU /
  Heston / ...),
- `model_h` — the jump distribution `h`,
- `model_psi` — the waiting-time density `ψ`,
- `suppress_n0` - allows the supression of the first contribution shaped as a delta,
- `time_range_long` - allows to focuss either in short or long time ranges,
- `parameters_literature` - settles the procedure of the parameters, being the OLS estimation or the literature.


Each is clearly documented inline where it is defined; the descriptions above have the objective to let the reader know where to intervene without reading the whole source first.

## Acknowledgements
 
Developed at the University of Barcelona under the supervision of
Dr. J. Perelló.
 
## Contact
 
Hug Sala Oriol— hsalaoriol@gmail.com


