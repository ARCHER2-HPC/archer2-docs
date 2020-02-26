Using Python
============

Python on Cirrus is provided by the `Anaconda <https://www.continuum.io/>`__
distribution. Both Python 2 and Python 3 versions of the distributions are
supported.

The central installation provides many of the most common packges used for
scientific computation and data analysis.

If the packages you require are not included in the central Anaconda Python
distribution, then the simplest way to make these available is often to install
your own version of `Miniconda <https://conda.io/miniconda.html>`__  and add the packages you need. We provide 
instructions on how to do this below. An alternative way to provide your own
packages (and to make them available more generally to other people in your
project and beyond) would be to use a Singularity container, see the :doc:`containers`
chapter of this User Guide for more information on this topic.

Accessing central Anaconda Python
---------------------------------

Users have the standard system Python available by default. To setup your environment
to use the Anaconda distributions you should use:

::

    module load anaconda/python2

for Python 2, or:

::

    module load anaconda/python3

for Python 3.

You can verify the current version of Python with:

::

   [user@cirrus-login0 ~]$ module load anaconda/python2
   [user@cirrus-login0 ~]$ python --version
   Python 2.7.12 :: Anaconda 4.2.0 (64-bit)

Full details on the Anaconda distributions can be found on the Continuum website at:

* http://docs.continuum.io/anaconda/index.html

Packages included in Anaconda distributions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can list the packages currently available in the distribution you have loaded with the command conda list:

::

   [user@cirrus-login0 ~]$ module load anaconda
   [user@cirrus-login0 ~]$ conda list
   # packages in environment at /lustre/sw/anaconda/anaconda2:
   #
   _license                  1.1                      py27_1  
   alabaster                 0.7.10                   py27_0  
   anaconda                  custom                   py27_0  
   anaconda-client           1.6.3                    py27_0  
   anaconda-navigator        1.2.3                    py27_0  
   anaconda-project          0.6.0                    py27_0  
   argcomplete               1.0.0                    py27_1  
   asn1crypto                0.22.0                   py27_0  
   astroid                   1.4.9                    py27_0
   ...

Adding packages to the Anaconda distribution
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Adding packages to the central Anaconda distribution cannot be done by users. If you wish to have additional
packages, we recommend installing your own local version of Miniconda and adding the packages you need. This
approach is described in Custom Environment below.

Custom Environments
-------------------

To setup a custom Python environment including packages that are not in the central installation, the simplest
approach is the install Miniconda locally in your own directories.

Installing Miniconda
~~~~~~~~~~~~~~~~~~~~

First, you should download Miniconda. You can use ``wget`` to do this, for example:

::

   wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh

You can find links to the various miniconda versions on the Miniconda website:

* https://conda.io/miniconda.html

For Cirrus, you should use the Linux 64-bit (bash installer).

Once you have downloaded the installer, you can run it. For example:

::

   [user@cirrus-login0 ~]$ bash Miniconda3-latest-Linux-x86_64.sh 
   
   Welcome to Miniconda3 4.3.31
   
   In order to continue the installation process, please review the license
   agreement.
   Please, press ENTER to continue
   >>> 
   ====================================
   Miniconda End User License Agreement
   ====================================
   
   ---snip---
   
   
   Do you accept the license terms? [yes|no]
   [no] >>> yes
   
   Miniconda3 will now be installed into this location:
   /lustre/home/t01/user/miniconda3
   
     - Press ENTER to confirm the location
     - Press CTRL-C to abort the installation
     - Or specify a different location below
   
   [/lustre/home/t01/user/miniconda3] >>> 
   PREFIX=/lustre/home/t01/user/miniconda3
   installing: python-3.6.3-h6c0c0dc_5 ...
   installing: ca-certificates-2017.08.26-h1d4fec5_0 ...
   installing: conda-env-2.6.0-h36134e3_1 ...
   installing: libgcc-ng-7.2.0-h7cc24e2_2 ...
   installing: libstdcxx-ng-7.2.0-h7a57d05_2 ...
   installing: libffi-3.2.1-hd88cf55_4 ...
   installing: ncurses-6.0-h9df7e31_2 ...
   installing: openssl-1.0.2n-hb7f436b_0 ...
   installing: tk-8.6.7-hc745277_3 ...
   installing: xz-5.2.3-h55aa19d_2 ...
   installing: yaml-0.1.7-had09818_2 ...
   installing: zlib-1.2.11-ha838bed_2 ...
   installing: libedit-3.1-heed3624_0 ...
   installing: readline-7.0-ha6073c6_4 ...
   installing: sqlite-3.20.1-hb898158_2 ...
   installing: asn1crypto-0.23.0-py36h4639342_0 ...
   installing: certifi-2017.11.5-py36hf29ccca_0 ...
   installing: chardet-3.0.4-py36h0f667ec_1 ...
   installing: idna-2.6-py36h82fb2a8_1 ...
   installing: pycosat-0.6.3-py36h0a5515d_0 ...
   installing: pycparser-2.18-py36hf9f622e_1 ...
   installing: pysocks-1.6.7-py36hd97a5b1_1 ...
   installing: ruamel_yaml-0.11.14-py36ha2fb22d_2 ...
   installing: six-1.11.0-py36h372c433_1 ...
   installing: cffi-1.11.2-py36h2825082_0 ...
   installing: setuptools-36.5.0-py36he42e2e1_0 ...
   installing: cryptography-2.1.4-py36hd09be54_0 ...
   installing: wheel-0.30.0-py36hfd4bba0_1 ...
   installing: pip-9.0.1-py36h6c6f9ce_4 ...
   installing: pyopenssl-17.5.0-py36h20ba746_0 ...
   installing: urllib3-1.22-py36hbe7ace6_0 ...
   installing: requests-2.18.4-py36he2e5f8d_1 ...
   installing: conda-4.3.31-py36_0 ...
   installation finished.
   WARNING:
       You currently have a PYTHONPATH environment variable set. This may cause
       unexpected behavior when running the Python interpreter in Miniconda3.
       For best results, please verify that your PYTHONPATH only points to
       directories of packages that are compatible with the Python interpreter
       in Miniconda3: /lustre/home/t01/user/miniconda3
   Do you wish the installer to prepend the Miniconda3 install location
   to PATH in your /lustre/home/t01/user/.bashrc ? [yes|no]
   [no] >>> 
   
   You may wish to edit your .bashrc to prepend the Miniconda3 install location to PATH:
   
   export PATH=/lustre/home/t01/user/miniconda3/bin:$PATH
   
   Thank you for installing Miniconda3!

Miniconda is now installed in your local directories but we still need to setup a way to access it
correctly. There are a number of ways to do this.

* If you are always going to be using this Python environment on ARCHER and do not wish to use any
  other Python environment, you can follow the advice of the Miniconda installer and add a line to
  your ``.bashrc`` file
* You can export PATH every time you wish to use you local install by using the bash command
  ``export PATH=/lustre/home/t01/user/miniconda3/bin:$PATH`` (using the correct PATH as specified by
  the installer). This will become tedious if you use the environment often!
* You can create an alias in your `.bashrc` file to set the path. For example, adding the line
  ``alias condasetup="export PATH=/lustre/home/t01/user/miniconda3/bin:$PATH"`` would allow you to
  use the command ``condasetup`` to initialise the Miniconda environment.
* You could also create a modulefile to provide a way to initialise the environment using
  ``module load ...`` as we do for our Anaconda environments. Please contact the helpdesk if you want help to do this.

Installing packages into Miniconda
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once you have installed Miniconda and setup your environment to access it, you can then add whatever packages
you wish to the installation using the ``conda install ...`` command. For example:

::

   [user@cirrus-login0 ~]$ conda install numpy
   Fetching package metadata ...............
   Solving package specifications: .
   
   Package plan for installation in environment /lustre/home/t01/user/miniconda3:
   
   The following NEW packages will be INSTALLED:
   
       blas:        1.1-openblas                  conda-forge
       libgfortran: 3.0.0-1                                  
       numpy:       1.14.0-py36_blas_openblas_200 conda-forge [blas_openblas]
       openblas:    0.2.20-7                      conda-forge
   
   The following packages will be UPDATED:
   
       conda:       4.3.31-py36_0                             --> 4.3.33-py36_0 conda-forge
   
   The following packages will be SUPERSEDED by a higher-priority channel:
   
       conda-env:   2.6.0-h36134e3_1                          --> 2.6.0-0       conda-forge
   
   Proceed ([y]/n)? y
   
   conda-env-2.6. 100% |########################################################################| Time: 0:00:00  33.71 kB/s
   libgfortran-3. 100% |########################################################################| Time: 0:00:00   7.85 MB/s
   openblas-0.2.2 100% |########################################################################| Time: 0:00:03   4.84 MB/s
   blas-1.1-openb 100% |########################################################################| Time: 0:00:00   1.33 MB/s
   numpy-1.14.0-p 100% |########################################################################| Time: 0:00:01   5.00 MB/s
   conda-4.3.33-p 100% |########################################################################| Time: 0:00:00   5.71 MB/s
   Here we see the numpy module has been installed in the local environment:
   
   [user@cirrus-login0 ~]$ conda list
   # packages in environment at /lustre/home/t01/user/miniconda3:
   #
   asn1crypto                0.23.0           py36h4639342_0  
   blas                      1.1                    openblas    conda-forge
   ca-certificates           2017.08.26           h1d4fec5_0  
   certifi                   2017.11.5        py36hf29ccca_0  
   cffi                      1.11.2           py36h2825082_0  
   chardet                   3.0.4            py36h0f667ec_1  
   conda                     4.3.33                   py36_0    conda-forge
   conda-env                 2.6.0                         0    conda-forge
   cryptography              2.1.4            py36hd09be54_0  
   idna                      2.6              py36h82fb2a8_1  
   libedit                   3.1                  heed3624_0  
   libffi                    3.2.1                hd88cf55_4  
   libgcc-ng                 7.2.0                h7cc24e2_2  
   libgfortran               3.0.0                         1  
   libstdcxx-ng              7.2.0                h7a57d05_2  
   ncurses                   6.0                  h9df7e31_2  
   numpy                     1.14.0          py36_blas_openblas_200  [blas_openblas]  conda-forge
   openblas                  0.2.20                        7    conda-forge
   openssl                   1.0.2n               hb7f436b_0  
   pip                       9.0.1            py36h6c6f9ce_4  
   pycosat                   0.6.3            py36h0a5515d_0  
   pycparser                 2.18             py36hf9f622e_1  
   pyopenssl                 17.5.0           py36h20ba746_0  
   pysocks                   1.6.7            py36hd97a5b1_1  
   python                    3.6.3                h6c0c0dc_5  
   readline                  7.0                  ha6073c6_4  
   requests                  2.18.4           py36he2e5f8d_1  
   ruamel_yaml               0.11.14          py36ha2fb22d_2  
   setuptools                36.5.0           py36he42e2e1_0  
   six                       1.11.0           py36h372c433_1  
   sqlite                    3.20.1               hb898158_2  
   tk                        8.6.7                hc745277_3  
   urllib3                   1.22             py36hbe7ace6_0  
   wheel                     0.30.0           py36hfd4bba0_1  
   xz                        5.2.3                h55aa19d_2  
   yaml                      0.1.7                had09818_2  
   zlib                      1.2.11               ha838bed_2  

Please note, for some package installations it may also be necessary to specify a channel such as conda-forge.
For example, the following command installs the pygobject module.

::

   [user@cirrus-login0 ~]$ conda install -c conda-forge pygobject 

