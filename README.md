## Initiative 31: Documents

This repository contains documents persisting meeting slides, insights, and assessments of the Initiative 31.

### General Information

Some general information about the initiative, it's motivation, goals and setup can be found in the [kick-off slides](meetings/2024-05-17%20Kick-Off.pptx).

### Results

Results of the prototyping work in the initiative can be found in the `results` folder. The different technologies with links to their specific information are:
- [Skia/VCL](results/skia_vcl.md): Prototype using the Visual Class Library (VCL), the widget library from LibreOffice, together with Skia as a rendering engine
- [Skia/Custom-Drawn](results/custom.md): Prototype using widgets that are custom-drawn with primitive drawing operations using either the OS-native rendering engines or Skia with the Java bindings provided by Skija
- Swing _(link to be added)_: Prototype using the Java-integrated UI framework Swing
- GTK _(link to be added)_: Prototype using GTK, as already used for the Linux SWT implementation, with its Windows and MacOS ports to reuse the existing SWT GTK implementation on all three major operating systems