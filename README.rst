ARCHER2 Documentation
=====================

**Note: The ARCHER2 Service is not yet available. This documentation is in
development. ARCHER2 is due to commence operation in 2020, replacing
the current service ARCHER. For more information on ARCHER, please
visit the `ARCHER web site <http://www.archer.ac.uk>`__.**

ARCHER2 is the next generation UK National Supercomputing Service. You can find more
information on the service and the research it supports on the
`ARCHER2 website <https://www.archer2.ac.uk>`__.

This repository contains the documentation for the service and is linked to a rendered version
currently hosted on Github pages.

For a guide on the rst file format see
`this <http://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html>`_ document.

This documentation is drawn from the `Cirrus documentation <https://github.com/EPCCed/cirrus-docs>`_,
`Sheffield Iceberg documentation <https://github.com/rcgsheffield/sheffield_hpc>`_ 
and the `ARCHER <http://www.archer.ac.uk>`_ documentation.

Rendered documentation
----------------------

* `ARCHER2 Documentation (HTML) <https://docs.archer2.ac.uk>`_


How to contribute
-----------------

We welcome contributions that improve the documentation from the ARCHER2 community and the
wider HPC world. Contributions can take the form of improved or additional content and/or
Issues that identify problems or opportunities for improvement.

**Note:** All interactions on the ARCHER2 documentation repository should follow the 
`ARCHER2 Code of Conduct <https://www.archer2.ac.uk/training/code-of-conduct/>`__
so that we, as a community, provide a welcoming and supportive environment for all people,
regardless of background or identity. 

To contribute content to this documentation, first you have to fork it on GitHub and clone it
to your machine, see `Fork a Repo <https://help.github.com/articles/fork-a-repo/>`_ for the
GitHub documentation on this process.

Once you have the git repository locally on your computer, you will need to install
``sphinx`` to be able to build the documentation. See the instructions below for how to achieve this.

Once you have made your changes and updated your Fork on GitHub you will need to
`Open a Pull Request <https://help.github.com/articles/using-pull-requests/>`_.

Building the documentation on a local Windows machine
#####################################################

Install the following:-

* `Anaconda Python <https://store.continuum.io/cshop/anaconda>`_.

To build the HTML documentation run::

    make html


Building the documentation on a local Linux machine
###################################################

Have

* Python 3
* sphinx

installed, then run ::

     make html

Building the documentation on a local Mac machine
#################################################

For the HTML documentation you will need ``sphinx``. If you do not already have a
python distribution installed, we recommend you install
`Anaconda Python <https://store.continuum.io/cshop/anaconda>`_.

To build the HTML documentation run::

    make html


Making changes and style guide
------------------------------

The documentation consists of a series of `reStructured Text <http://sphinx-doc.org/rest.html>`_ files which have the ``.rst`` extension.
These files are then automatically converted to HTMl and combined into the web version of the documentation by sphinx.
It is important that when editing the files the syntax of the rst files is followed.
If there are any errors in your changes the build will fail and the documentation  will not update, you can test your build locally by running ``make html``.
The easiest way to learn what files should look like is to read the ``rst`` files already in the repository.

A short list of style guidance:

* Headings should be in sentance case


