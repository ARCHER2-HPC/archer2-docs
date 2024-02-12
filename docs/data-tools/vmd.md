# VMD

[VMD][vmd-website] is a visualisation program for displaying, animating, and analysing molecular systems
using 3D graphics, and built-In tcl/tk scripting.

## Useful links

  - [VMD website][vmd-website]
  - [VMD build instructions][vmd-build]


## Using VMD on ARCHER2

VMD is available through the `vmd` module.

```bash
module load vmd
```

Once the module has been added the VMD executables,
tools, and libraries will be made available.

Without anything else, this allows you to run VMD in "text-only" mode with:

```bash
vmd -dispdev text
```

If you want to launch VMD with a GUI, see the requirements on the next section.

### Launching VMD with a GUI

To be able to launch VMD with it's graphical interface,
your machine needs to support the x11 "X windows system".
Most Linux and \*NIX systems support this by default.
If you're using Windows (through [WSL][wsl], for example),
you will need an X11 display server, we recommend [XMing][xming].
For macOS, we recommend [XQuartz][xquartz],
but please be aware that there's some extra configuration needed, please see the next section

To launch VMD with a GUI, once you have a running X11 display server on your local machine,
you'll need to connect to ARCHER2 with X11 forwarding enabled,
please follow the instructions in [the logging in section][archer2-docs-login].
Once you're connected to ARCHER2, load the VMD module with:

```bash
module load vmd
```

and launch VMD with:

```bash
vmd
```

### Using VMD from macOS

If you're using macOS and XQuartz, before you're able to launch VMD with a GUI,
you will need to change the XQuartz configuration.
On a local terminal (that is, not connected to ARCHER2), run the following command:

```bash
defaults write org.xquartz.X11 enable_iglx -bool true
```

then, restart XQuartz.
You will now be able to launch VMD's GUI without a `segmentation fault`.


## Compiling VMD

The latest instructions for building VMD on ARCHER2 may be found in
the [GitHub repository of build instructions][vmd-build].



[archer2-docs-login]: ../user-guide/connecting.md#logging-in "ARCHER2 documentation -- logging in"
[vmd-build]: https://github.com/hpc-uk/build-instructions/tree/main/utils/vmd "VMD compilation instructions"
[vmd-website]: https://www.ks.uiuc.edu/Research/vmd/ "VMD website"
[wsl]: https://learn.microsoft.com/en-us/windows/wsl/install "Windows subsystem for Linux"
[xming]: https://en.wikipedia.org/wiki/Xming "XMing X11 display server"
[xquartz]: https://en.wikipedia.org/wiki/XQuartz "XQuartz x11 display server"
