## Overview

[![ROOT](https://img.shields.io/badge/ROOT-v6.28/04-blue)](https://root.cern.ch/)
[![TUFA](https://img.shields.io/badge/TUFA-v0.1-blue)](https://github.com/peppapiggyme/ttbar-unfolding)
[![CMake](https://github.com/peppapiggyme/ttbar-unfolding/actions/workflows/cmake.yml/badge.svg)](https://github.com/peppapiggyme/ttbar-unfolding/actions/workflows/cmake.yml)

Collection of works on unfolding practices using $t\bar{t}$ NLO samples.

* `Author`: Bowen Zhang
* `Data`: 10/06/2023
* `Version`: 0.1
* `Workspace`: Artlas:~/Documents/projects/ttbar-unfolding/

> See also https://trello.com/c/UBuyDELV

## Samples

**[MG5PY8 $t\bar{t}$ samples]** The matrix elements are generated by `MadGraph5 aMC@NLO`, $t\bar{t}$ decays to $bW^{+}\bar{b}bW^{-}$ to $b\mu^{+}\nu_{\mu}\bar{b}b\mu^{-}\bar{\nu}_{\mu}$ using `MadSpin`. The samples are showered by `Pythia8`. The output Hepmc file is reconstructed by fast detector simulation tool `Delphes`.

## Structure

* `RooUnfold` (external): Roofit extension. Interface to common unfolding methods implemented in ROOT.
* `ttbar_MadTree`: Tool to analysis the MG5PY8 $t\bar{t}$ samples. Fill trees and histograms.
* `ttbar_Unfold`: Application of RooUnfold with kinematic distributions produced by ttbar_MadTree.
* ...

## Details

### Sample generation

* Madgraph aMC@NLO + Pythia8 + MadSpin + Delphes
* Artlas: ~/Documents/playground/ttbar_nlo/
* Pre-selection ttbar_nlo_decayed

| Split | Status | Size | File |
| --    | --     | --   | --   |
| Train | ![done](resources/status-done-brightgreen.svg) | 100k | ttbar_nlo_ATLAS_PileUp.root      |
| Test  | ![done](resources/status-done-brightgreen.svg) | 100k | ttbar_nlo_ATLAS_PileUp_Test.root |

 100k for training , 100k for testing 

### MadTree analysis
* Truth: Find two original top-quark (by status == 62). Set them as true top-quarks.
```c++
if (tree->Particle_Status[i] == 62) {
    if (tree->Particle_PID[i] == 6) {
        t[0] = i;
        ++count;
    } else if (tree->Particle_PID[i] == -6) {
        t[1] = i;
        ++count;
    }
}
```
* Reco: (bbmumu) Find two b-jets (efficiency is quite low..). Find two muons (leading two..)
* Targetting kinematics: sum of transverse momentum (true and reconstructed $p_{\text{T}}^{t\bar{t}}$)
>
> Looks like the opposite of unfolding: inverse smearing..
> 
> <img src="resources/dist.png" alt="Distribution" height="240px"/>
>
* Fill/Miss response in ttbar_MadTree (![pending](resources/status-pending-orange.svg))
* Output: reco_analysis__ttbar_nlo_ATLAS_PileUp.root
* Produce ROOT tree. Might be utilised by advanced methods.
```
root [1] nominal->Show(1)
======> EVENT:1
t0_truth_Pt     = 166.36
t1_truth_Pt     = 207.932
tt_truth_Pt     = 44.5957
tt_truth_m      = 513.26
ST_truth        = 374.292
tt_Pt           = 155.199
tt_m            = 284.667
ST              = 464.953
mu0_Pt          = 49.5792
mu1_Pt          = 10.8683
b0_Pt           = 150.474
b1_Pt           = 72.6333
MET             = 181.398
n_jets          = 7
event_number    = 2
weight          = 0.00154058
```

### Unfolding

* Tutorial: follow this [RooUnfold tutorial](https://statisticalmethods.web.cern.ch/StatisticalMethods/unfolding/RooUnfold_01-Methods/).
> 
> <img src="resources/unfolding.png" alt="Tutorial" height="240px"/>
> 
* Other reference: [TUnfold](https://root.cern.ch/doc/master/classTUnfold.html) | [TSVDUnfold](https://root.cern/doc/v628/classTSVDUnfold.html) | [TUnfold tutorial](https://root.cern.ch/doc/master/group__tutorial__unfold.html) | [RooUnfold](https://gitlab.cern.ch/RooUnfold/RooUnfold)
* Traditional method: choose SVD method
* Normalising flow unfolding (![pending](resources/status-pending-orange.svg))
* Performance metric (![pending](resources/status-pending-orange.svg))
