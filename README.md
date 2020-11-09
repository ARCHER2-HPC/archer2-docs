# ARCHER2 Documentation

\*\*Note: The ARCHER2 Service is not yet available. This documentation
is in development. ARCHER2 is due to commence operation in 2020,
replacing the current service ARCHER. For more information on ARCHER,
please visit the [ARCHER web site](http://www.archer.ac.uk).\*\*

ARCHER2 is the next generation UK National Supercomputing Service. You
can find more information on the service and the research it supports on
the [ARCHER2 website](https://www.archer2.ac.uk).

This repository contains the documentation for the service and is linked
to a rendered version currently hosted on Github pages.

For a guide on the rst file format see
[this](http://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html)
document.

This documentation is drawn from the [Cirrus
documentation](https://github.com/EPCCed/cirrus-docs), [Sheffield
Iceberg documentation](https://github.com/rcgsheffield/sheffield_hpc)
and the [ARCHER](http://www.archer.ac.uk) documentation.

## Rendered documentation

  - [ARCHER2 Documentation (HTML)](https://docs.archer2.ac.uk)

## How to contribute

We welcome contributions that improve the documentation from the ARCHER2
community and the wider HPC world. Contributions can take the form of
improved or additional content and/or Issues that identify problems or
opportunities for improvement.

**Note:** All interactions on the ARCHER2 documentation repository
should follow the [ARCHER2 Code of
Conduct](https://www.archer2.ac.uk/training/code-of-conduct/) so that
we, as a community, provide a welcoming and supportive environment for
all people, regardless of background or identity.

To contribute content to this documentation, first you have to fork it
on GitHub and clone it to your machine, see [Fork a
Repo](https://help.github.com/articles/fork-a-repo/) for the GitHub
documentation on this process.

Once you have the git repository locally on your computer, you will need
to [install Material for mkdocs](https://squidfunk.github.io/mkdocs-material/getting-started/) to be able to build the documentation. This can be done using a local installation
or using a Docker container.

Once you have made your changes and updated your Fork on GitHub you will
need to [Open a Pull
Request](https://help.github.com/articles/using-pull-requests/).

### Building the documentation on a local machine

Once Material for mkdocs is installed, you can preview the site locally using the
[instructions in the Material for mkdocs documentation](https://squidfunk.github.io/mkdocs-material/creating-your-site/#previewing-as-you-write).


## Making changes and style guide

The documentation consists of a series of Markdown files which have the `.md`
extension. These files are then automatically converted to HTMl and
combined into the web version of the documentation by mkdocs. It is
important that when editing the files the syntax of the Markdown files is
followed. If there are any errors in your changes the build will fail
and the documentation will not update, you can test your build locally
by running `mkdocs serve`. The easiest way to learn what files should look
like is to read the Markdown files already in the repository.

A short list of style guidance:

  - Headings should be in sentance case
