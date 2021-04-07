# Essential Skills

This section provides information and links on essential skills required to use ARCHER2 efficiently: e.g. using Linux command line, accessing help and documentation.


## Terminal

In order to access HPC machines such as ARCHER2 you will need to use a Linux command line terminal window

Options for Linux, MacOS and Windows are described under our [Connecting to ARCHER2 guide](https://docs.archer2.ac.uk/user-guide/connecting/)

## Linux Command Line

A guide to using the [Unix Shell for complete novices](https://swcarpentry.github.io/shell-novice/)

For those already familiar with the basics there is also a lesson on [shell extras](https://carpentries-incubator.github.io/shell-extras/)

## Basic Slurm commands

Slurm is the scheduler used on ARCHER2 and we provide a guide to using the [basic Slurm commands](https://docs.archer2.ac.uk/user-guide/scheduler/#basic-slurm-commands) including how to find out:
 
- what resources are available to you 
- how to submit jobs to the scheduler  
- the status of jobs submitted

## Text Editors

The following text editors are available on ARCHER2

| Name | Description | Examples |
| ---  | ---         | ---      |
| emacs | A widely used editor <br /> with a focus on extensibility. | `emacs    -nw    sharpen.pbs` <br /> `CTRL+X CTRL+C` quits  <br /> `CTRL+X CTRL+S` saves
| nano | A small, free editor  <br />with a focus on user friendliness.|`nano sharpen.pbs` <br /> `CTRL+X` quits <br /> `CTRL+O` saves|
| vi | A mode based editor <br /> with a focus on aiding code development. | `vi cfd.f90`  <br /> `:q` in command mode quits  <br /> `:q!` in command mode quits without saving  <br />`:w` in command mode saves  <br /> `i` in command mode switches to insert mode  <br /> `ESC` in insert mode switches to command mode |

If you are using MobaXterm on Windows you can use the inbuilt MobaTextEditor text file editor.

You can edit on your local machine using your preferred text editor, and then upload the file to ARCHER2.  Make sure you can save the file using Linux line-endings. Notepad, for example, will support Unix/Linux line endings (LF), Macintosh line endings (CR), and Windows Line endings (CRLF) 


## Quick Reference Sheet

We have produced this [Quick Reference Sheet](Quick-Reference-Sheet.pdf) which you may find useful.


