# Community Earth System Model (CESM) version 2.1.3

[CESM2](https://www.cesm.ucar.edu/models/cesm2/) is a fully-coupled, community, global climate model that provides state-of-the-art computer simulations of the Earth's past, present, and future climate states. It has seven different components: atmosphere, ocean, river run off, sea ice, land ice, waves and adaptive river transport.  At the time of writing, CESM 2.1.3 is the latest scientifically verified version of the model.

!!! important
    CESM is not part of the officially supported
    software on ARCHER2. While the ARCHER2 service desk is able to provide
    support for basic use of this software (e.g. access to software, writing
    job submission scripts) it does not generally provide detailed technical
    support for the software and you may be directed to seek support from
    other places if the service desk cannot answer the questions.

## Setting up CESM 2.1.3 on ARCHER2

Due to the nature of CESM2, there is not a centrally installed version of the program available on ARCHER2. Instead, users download their own copy of the program and make use of ARCHER2-specific configurations that have been rigorously tested.

The setup process has been streamlined on ARCHER2 and can be carried out by following the instructions on the [ARCHER2 CESM2.1.3 setup](cesm213_setup.md) page

## Using CESM 2.1.3 on ARCHER2

A [quickstart guide](cesm213_run.md) for running a simple coupled case of CESM 2.1.3 on ARCHER2 can be found here. It should be noted that this is only a quickstart guide with a focus on the way that CESM 2.1.3 should be run specifically on ARCHER2, and is not intended to replace the larger CESM or CIME documentation linked to below.

## Useful Links

### Documentation

If this is your first time running CESM2, it is highly recommended that you consult both the [CIME documentation](http://esmci.github.io/cime/versions/maint-5.6/html/) and the [NCAR CESM pages](https://escomp.github.io/CESM/versions/cesm2.1/html/introduction.html) for the version used in CESM 2.1.3, paying particular attention to the pages on [Basic Usage of CIME](http://esmci.github.io/cime/versions/maint-5.6/html/users_guide/index.html) which gives detailed description of the basic commands needed to get a model running.

### Compsets and Configurations

CESM2 allows simulations to be carried out using a very wide range of configurations. If you are new to CESM2 it is highly recommended that, unless you are running a case you are already familiar with, you consult the [CESM2.1 Configurations](https://escomp.github.io/CESM/versions/cesm2.1/html/cesm_configurations.html) page. You can also see a list of the defined compsets already available on the [component set definitions](https://www.cesm.ucar.edu/models/cesm2/config/2.1.3/compsets.html) page. More information about configurations, grids and compsets can be found on the [CESM2 Configurations and Grids](https://www.cesm.ucar.edu/models/cesm2/config/) page, which includes links to the configuration settings of the different components.
