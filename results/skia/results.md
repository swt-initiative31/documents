# LibreOffice VCL/UNO Results

## Assumptions

1. Only the Skia implementation of VCL is important for our project, but there are also many others like GTK, QT, Windows, macOS.
2. Native OS theming is not important

## General Results

It seems LibreOffice want to used widget toolkits (like GTK) to render OS-native UIs. 
However, they are hindered by available extensions (using UNO) to get rid of VCL completely (at least in midterm future).

For LibreOffice development, it is recommendet to define UIs in `.ui` files defined with GtkBuilder.
These files can be used by GTK (and a generic implementation for VCL) to render the UI. 

## Approaches

### Native VCL:

- LibreOffice consider VCL not as stable API/ABI. 
Code changes to the VCL code could break the API which might require adaptations.
- The native VCL implementation in /vcl/toolkit is deprecated.
- Theoretically VCL can be still used using following approach:
    - The UI can be started from Java using the FFM API via a C-APi. 
    Further C-calls (also using the FFM API) could be used to control what is displayed on the UI.
    - The C-API <-> Java FFM API communication would need to be constructed.

### UNO API

UNO APIs are not designed fot a widget toolkit but to build extensions for LibreOffice.
However, the UNO APIs also provide functioanlity to create a graphical user interface

APIs can be used in varios programming environments such as C/C++, Python and Java.
This means, it was possible to call UNO APIs from Java directly. 

When using UNO, the APIs call into a running LibreOffice process to extend LibreOffice by new functionality.
If LibreOffice was not started, it would be started by a bootstrap call.
Therefore, the LibreOffice code would need to be reduced to a usable UI process tool that can be started from Java.

The UNO-API is relatively difficult to use and develop with. 
It is often unclear when objects can be requested via queryInterface and when not.
For example, XSimpleCanvas cannot be instantiated directly and only returns a null object. 
XGraphics is used instead.

Further findings: 
- UNO APIs do not seem to be sufficient for all use cases, yet. E.g., the window header cannot be changed.
- The API reference documentation,is not very extensive. 


#### Two-Process Approach:

As mentioned before, the two-process approach is the default for UNO APIs.
A full-blown LibreOffice is running in one process. 
Extensions are running in a second process and using UNO APIs to call into the LibreOffice's process.

In general, a two-process approach raises the questions how much worse would be the performance and resource consumption (RAM, disk space)?
A performance analysis would be meaningful.

Furthermore, to avoid a full-blown LibreOffice running in the process, a minimal application has to be built that allows 
1. to render a graphical user interface and
2. support the interprocess communication of UNO's APIs

Furthermore, additional code changes might also be necessary for this UI processing application.
It needs to be maintained constantly. 
Any code changes to LibreOffice could result in adaptation efforts in the UI processing application.


#### Single-Process UNO Approach:

A conversion to a single process is possible but not intended by LibreOffice.
The procedure would look as follows:


#####  Design

Communication between Java and the UI code would occur through an "external C" API addressed via Java FFM API.
This "C-API/FFM API" layer would need to be constructed. 
It would then communicate with the UNO API directly without an IPC Bridge.
??? This is not intended as an API, which means that changes to LibreOffice code could potentially requires adaptation of the code.

##### Execution

To start the UI, the UNO-UI is started from Java via a C-Call.
This call executes an application, similar to `/desktop/source/app/app.cxx` with the method `Desktop::Main()`.
Further calls of the constructed C-API could be performed from other threads in Java to instruct the UNO-API which widgets to display.
This means the constructed C-API would need to be as powerful as the UNO-API.

However, this C-API could also make calls to the VCL layer, which introduces potential vulnerability to LibreOffice code changes.

It should be possible to use upcalls to perform Java methods in the main thread.
