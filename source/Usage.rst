.. _Usage:

Usage
===============

This page explains how to use SuperElastix.

Commandline tool
----------------

The SuperElastix commandline tool can be found at:
  
- Windows: :code:`<build-path>\Applications-build\CommandLineInterface\[Release|Debug]\ `

- Linux: :code:`<build-path>/Applications-build/CommandLineInterface/` 

Demo Experiments
----------------

To run the demo experiments SuperElastix needs to be installed:

- Windows:

  - open :code:`<build-path>\Applications-build\SuperElastixApplications.sln`
  - in project solution explorer right-click on Demo -> Project Only -> Build Only Demo
  
- Linux: 

  - change dir to :code:`<build-path>/Applications-build/`
  - :code:`make Demo`

By building the :code:`Demo` target the SuperElastix executable, image data, configuration files and commandline scripts will be copied to the :code:`<DEMO_PREFIX>` directory. The :code:`<DEMO_PREFIX>` can be set by CMake and defaults to :code:`<build-path>/SuperElastixApplications-build/Demo`
These four scripts run the Demo experiments as described in [1]_:

- :code:`1A_SuperElastix_elastix_NC`
- :code:`1B_SuperElastix_elastix_MSD`
- :code:`2A_SuperElastix_itkv4_NC`
- :code:`2B_SuperElastix_itkv4_MSD`

.. [1] *The design of SuperElastix - a unifying framework for a wide range of image registration methodologies, F. Berendsen, K. Marstal, S. Klein and M. Staring*, found at :code:`<source-path>\Documentation\source\paperWBIR16`.


Library use
-----------

SuperElastix supports two ways of library usage: 

- :code:`SuperElastixFilter`: A precompiled SuperElastixFilter library loaded with a default set of components. To link against this library a small number of header files are needed. Compilation time is limited, since most functionality is precompiled and not exposed during compilation of the application (pimpl idiom)

- :code:`SuperElastixCustomComponents<ComponentTypeList<...>>`: A templated SuperElastixFilter class with a user selectable set of components. The set of components can be of any number and of any variation of each component its template arguments, e.g. dimensionality, pixeltype or other template arguments. New user-developed components can be added to the ComponentTypeList and compiled into the filter without the need of changing any of the SuperElastix source codes. Compilation is longer and it requires the project to include all header of SuperElastix and the selected components. 

The SuperElastix commandline tool in :code:`<source-path>\Applications` serves as an example of the usage of the SuperElastix library. More details can be found in the next section.
