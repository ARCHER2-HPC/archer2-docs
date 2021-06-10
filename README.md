# ARCHER2 Documentation

ARCHER2 is the next generation UK National Supercomputing Service. You
can find more information on the service and the research it supports on
the [ARCHER2 website](https://www.archer2.ac.uk).

This repository contains the documentation for the service and is linked
to a rendered version currently hosted on Github pages.

This documentation is drawn from the [Cirrus
documentation](https://github.com/EPCCed/cirrus-docs), [Sheffield
Iceberg documentation](https://github.com/rcgsheffield/sheffield_hpc)
and the [ARCHER](http://www.archer.ac.uk) documentation.

## Rendered documentation

  - [ARCHER2 Documentation (HTML)](https://docs.archer2.ac.uk)

## How to contribute

We welcome contributions from the ARCHER2 community and beyond. Contributions can take many different
forms, some examples are:

- Raising Issues if you spot a mistake or something that could be improved
- Adding/updating material via a Pull Request
- Adding your thoughts and ideas to any open issues

All people who contribute and interact via this Github repository undertake to abide by the
[ARCHER2 Code of Conduct](https://www.archer2.ac.uk/about/policies/code-of-conduct.html) so that
we, as a community, provide a welcoming and supportive environment for
all people, regardless of background or identity.

To contribute content to this documentation, first you have to fork it
on GitHub and clone it to your machine, see [Fork a
Repo](https://help.github.com/articles/fork-a-repo/) for the GitHub
documentation on this process.

Once you have the git repository locally on your computer, you will need
to [install Material for mkdocs](https://squidfunk.github.io/mkdocs-material/getting-started/)
to be able to build the documentation. This can be done using a local installation
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
