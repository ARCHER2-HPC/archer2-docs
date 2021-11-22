## R for statistical computing

[R](https://www.r-project.org/) is a software environment for
statistical computing and graphics. It provides a wide variety of
statistical and graphical techniques (linear and nonlinear modelling,
statistical tests, time-series analysis, classification, clustering,
and so on).

!!! note
    When you log onto ARCHER2, no R module is loaded by
    default. You need to load the `cray-R` module to access the
    functionality described below.

The recommended version of R to use on ARCHER2 is the HPE Cray R
distribution, which can be loaded using:

```
module load cray-R
```

The HPE Cray R distribution includes a range of common R packages, including all of the base packages, plus a few others.

To see what packages are available, run the R command

```
library()
```

--from the R command prompt.

At the time of writing, the HPE Cray R distribution included the following packages:

=== "Full System"
   ```
   Packages in library ‘/opt/R/4.0.3.0/lib64/R/library’:
   
   base                    The R Base Package
   boot                    Bootstrap Functions (Originally by Angelo Canty
                           for S)
   class                   Functions for Classification
   cluster                 "Finding Groups in Data": Cluster Analysis
                           Extended Rousseeuw et al.
   codetools               Code Analysis Tools for R
   compiler                The R Compiler Package
   datasets                The R Datasets Package
   foreign                 Read Data Stored by 'Minitab', 'S', 'SAS',
                           'SPSS', 'Stata', 'Systat', 'Weka', 'dBase', ...
   graphics                The R Graphics Package
   grDevices               The R Graphics Devices and Support for Colours
                           and Fonts
   grid                    The Grid Graphics Package
   KernSmooth              Functions for Kernel Smoothing Supporting Wand
                           & Jones (1995)
   lattice                 Trellis Graphics for R
   MASS                    Support Functions and Datasets for Venables and
                           Ripley's MASS
   Matrix                  Sparse and Dense Matrix Classes and Methods
   methods                 Formal Methods and Classes
   mgcv                    Mixed GAM Computation Vehicle with Automatic
                           Smoothness Estimation
   nlme                    Linear and Nonlinear Mixed Effects Models
   nnet                    Feed-Forward Neural Networks and Multinomial
                           Log-Linear Models
   parallel                Support for Parallel computation in R
   rpart                   Recursive Partitioning and Regression Trees
   spatial                 Functions for Kriging and Point Pattern
                           Analysis
   splines                 Regression Spline Functions and Classes
   stats                   The R Stats Package
   stats4                  Statistical Functions using S4 Classes
   survival                Survival Analysis
   tcltk                   Tcl/Tk Interface
   tools                   Tools for Package Development
   utils                   The R Utils Package
   ```
=== "4-cabinet system"
   ```
   Packages in library ‘/opt/R/4.0.2.0/lib64/R/library’:
   
   base                    The R Base Package
   boot                    Bootstrap Functions (Originally by Angelo Canty
                           for S)
   class                   Functions for Classification
   cluster                 "Finding Groups in Data": Cluster Analysis
                           Extended Rousseeuw et al.
   codetools               Code Analysis Tools for R
   compiler                The R Compiler Package
   datasets                The R Datasets Package
   foreign                 Read Data Stored by 'Minitab', 'S', 'SAS',
                           'SPSS', 'Stata', 'Systat', 'Weka', 'dBase', ...
   graphics                The R Graphics Package
   grDevices               The R Graphics Devices and Support for Colours
                           and Fonts
   grid                    The Grid Graphics Package
   KernSmooth              Functions for Kernel Smoothing Supporting Wand
                           & Jones (1995)
   lattice                 Trellis Graphics for R
   MASS                    Support Functions and Datasets for Venables and
                           Ripley's MASS
   Matrix                  Sparse and Dense Matrix Classes and Methods
   methods                 Formal Methods and Classes
   mgcv                    Mixed GAM Computation Vehicle with Automatic
                           Smoothness Estimation
   nlme                    Linear and Nonlinear Mixed Effects Models
   nnet                    Feed-Forward Neural Networks and Multinomial
                           Log-Linear Models
   parallel                Support for Parallel computation in R
   rpart                   Recursive Partitioning and Regression Trees
   spatial                 Functions for Kriging and Point Pattern
                           Analysis
   splines                 Regression Spline Functions and Classes
   stats                   The R Stats Package
   stats4                  Statistical Functions using S4 Classes
   survival                Survival Analysis
   tcltk                   Tcl/Tk Interface
   tools                   Tools for Package Development
   utils                   The R Utils Package
   ```

## Running R on the compute nodes

In this section, we provide an example R job submission scripts for
using R on the ARCHER2 compute nodes.

### Serial R submission script

```
#!/bin/bash --login

#SBATCH --job-name=r_test
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=1
#SBATCH --time=00:10:00

# Replace [budget code] below with your project code (e.g., t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the batch environment
module load epcc-job-env

# Load the R module
module load cray-R

# Run your R progamme
Rscript serial_test.R
```

On completion, the output of the R script will be available in the job output file.
