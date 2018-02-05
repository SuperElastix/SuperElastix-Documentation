.. _CommandlineTool:

Commandline tool
================

This page explains how to use the SuperElastix executable.
The executable of the latest release can be downloaded from `GitHub releases <https://github.com/SuperElastix/SuperElastix/releases>`_.
Or, if SuperElastix was build from sources, it can be found at:
  
- Windows: :code:`<build-path>\Applications-build\CommandLineInterface\[Release|Debug]\ `

- Linux: :code:`<build-path>/Applications-build/CommandLineInterface/` 


By calling :code:`SuperElastix --help` its options are shown.

::

  Allowed options:
    --help                produce help message
    --conf arg            Configuration file: single or multiple Blueprints 
                          [.xml|.json]
    --in arg              Input data: images, labels, meshes, etc. Usage arg: 
                          <name>=<path> (or multiple pairs)
    --out arg             Output data: images, labels, meshes, etc. Usage arg: 
                          <name>=<path> (or multiple pairs)
    --graphout arg        Output Graphviz dot file
    --logfile arg         Log output file
    --loglevel arg        Log level [off|critical|error|warning|info|debug|trace]

The file format of a Blueprint is described in :ref:`design_configuring`, where the :code:`<name>`-s of the :code:`--in` and :code:`--out` arguments must correspond to the sinking and sourcing component identifier names as defined in the Blueprint, respectively.

Demo Experiments
----------------

To get started on how to invoke the SuperElastix executable we set up a few scripts that run demo registrations.  
These four scripts run the demo experiments as described in [1]_:

- :code:`1A_SuperElastix_elastix_NC`
- :code:`1B_SuperElastix_elastix_MSD`
- :code:`2A_SuperElastix_itkv4_NC`
- :code:`2B_SuperElastix_itkv4_MSD`

.. [1] *The design of SuperElastix - a unifying framework for a wide range of image registration methodologies, F. Berendsen, K. Marstal, S. Klein and M. Staring*, found at :code:`<source-path>\Documentation\source\paperWBIR16`.

The bash (linux) or batch (windows) scripts are bundled in the `release download <https://github.com/SuperElastix/SuperElastix/releases>`_.
Or, if SuperElastix was build from sources, follow these instructions to setup the scripts:

- Windows:

  - open :code:`<build-path>\Applications-build\SuperElastixApplications.sln`
  - in project solution explorer right-click on Demo -> Project Only -> Build Only Demo
  
- Linux: 

  - change dir to :code:`<build-path>/Applications-build/`
  - :code:`make Demo`

By building the :code:`Demo` target the SuperElastix executable, image data, configuration files and commandline scripts will be copied to the :code:`<DEMO_PREFIX>` directory. The :code:`<DEMO_PREFIX>` can be set by CMake and defaults to :code:`<build-path>/SuperElastixApplications-build/Demo`.

