# Setting up Sphinx to build local copy of ReadTheDocs pages #

This is a step-by-step guide for Windows10 users.

Install [Anaconda](https://www.anaconda.com/products/individual). (Am not going to cover that here)

Run Windows PowerShell as Administrator (Right-click Windows PowerShell in start menu, select 'Run as Administrator')

Check that it can see your Conda 

    conda list

should produce a long list of conda libraries.

    conda install sphinx

Confirm proceed when asked.

    conda list

should now include sphinx and several sphinxcontrib entries

Now also install the ReadTheDocs them

    conda install sphinx_rtd_theme

Confirm when asked

Now open a bash shell in the github/archer2-docs folder 

    ./make.bat html

Should run Sphinx and build the html files.

In explorer, navigate to 

github/archer2-docs/_build/html 


open index.html in browser to see the website.