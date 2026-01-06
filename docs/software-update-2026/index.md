# Refresh of Centrally Supported Software (January 2026) 

The CSE team has completed a review of the centrally supported software and will make changes during January 2026, deploying current versions of software and refreshing the list of application based on user demand.  

The full list of changes is appended below, though significant changes are, as follows: 

-    [ORCA ](https://www.faccts.de/orca/) will be added to the list of supported software to replace NWChem, which is being removed. 
-    [Code Saturne ](https://www.code-saturne.org/cms/web/) Version 9.0 will be installed. 
-    [QuantumEspresso ](https://www.quantum-espresso.org/) Versions 7.5 and Version 7.4.1 will be installed. 
-    [VASP ](https://www.vasp.at/) Version 6.5.1 will be installed (and made the default). A version with VTST will also be installed.  
-    [Blender ](https://www.blender.org/) will be updated to Version 5.0. 
-    [Climate Data Operators (CDO) ](https://code.mpimet.mpg.de/projects/cdo) will be updated to Version 2.5.4. 
-    [Paraview ](https://www.paraview.org/) will be updated to Version 6.0.1. 

The full list of changes is captured in the table below. If you have any questions or concerns, please contact the ARCHER2 Service Desk.  


| Software | Change  |
| --- | --- |
| **Applications**  |
| CASTEP | Default version set to Version 25.11 (was 24.1) |
| Code_Saturne | Add version 9.0 | 
| Py-ChemShell/Tcl-ChemShell | Remove Py-ChemShell Version 23.0.3 (deprecated) |
| CP2K | Add Version 2026.1 <br> Default changed to version 2025.2 (was 2024.3) |
| FHI-aims | Default changed to version 250822.0 (was 240920.0) <br> Add version 250822.0 |
| GROMACS | Add version 2024.7 and version 2025.4  <br> Default changed to Version 2025.4 |
| NAMD | Add Version 3.0.2 (and make default). Also add Version 3.0.2 (No SMP) and Version 3.0.2 (GPU) <br> Remove Version 2.14 and Version 3.0  |
| NWChem | Remove from list of supported packages - replace with ORCA |
| ORCA | Make ORCA a supported package to replace NWChem and install ORCA Version 6.1.0 (as default). <br> Version 5.0.3 and Version 6.0.0 already installed  |
| Quantum Espresso | Add versions 7.4.1 and 7.5 <br> Move default to Version 7.3.1 <br> Remove Version 6.8 |
| VASP | Install VASP 6.5.1 and make default <br> Also install VASP 6.5.1 with VTST |
| **Libraries** |
| Boost | Remove version 1.72.0 (old version) |
| HYPRE | Remove Version 2.18.0 (old version) |
| MUMPS | Remove Version 5.3.5 (old version) |
| NetCDF | Keep at Version 1.12.3.1/ 1.12.3.7, but update for Python 3.10 (cray-python) |
| PETSc | Remove Version 3.14.2 (old version) |
| Scotch | Remove Version 6.1.0 (old version) |
| SLEPC | Remove Version 3.14.1 (old version) |
| SuperLU_DIST | Remove Version 6.4.0 (old version) |
| VTK | New library added to support LAMMPS |
| **Analysis and Development Tools** |
| Blender | Update to Version 5.0 |
| Climate Data Operators (CDO) | Upgrade to Version 2.5.4. |
| Forge | Change default to Version 25.0.1 <br> Remove Version 22.1.3 |
| Paraview | Add Version 6.0.1  <br> Remove Versions 5.10.1 and 5.11.1 |
| PyTorch  |  Remove Version 1.10.2  <br> Upgrade CPU version to Version 2.9.1 (requires two modules: a source-code build for torch.distributed and a binary install that works with Horovod) <br> GPU version is dependent on ROCm version so must remain at Version 1.13.1 |
| SCons | Upgrade to Version 4.10.1 |
| Spindle | Upgrade to Version 0.14 |
| TensorFlow | Remove Version 2.9.3 |