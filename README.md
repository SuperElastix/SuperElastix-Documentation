Documentation for SuperElastix
==============================

This is the source code for the project documentation at [superelastix.readthedocs.org](http://superelastix.readthedocs.org/). The documentation is updated on every commit to the `Github repository <https://github.com/SuperElastix/SuperElastix-Documentation>`_. You can build it locally by installing sphinx: 

```bash
$ pip install sphinx
```

and run the following make command:

```bash
$ make html
```

The docs will be generated and the output files will be placed in the `build/html/` directory which can be browsed (locally) with your favorite browser. Optionally, to generate the uml diagrams run:

```bash
$ make html renderuml
```

This requires `plantuml.jar`, which can be downloaded from http://plantuml.com/download, to be in the `utils/` directory and java being installed.
The documentation can be automatically generated whenever a file changes (useful for development) using `sphinx-autobuild`:

```bash
$ make livehtml
```

Install `sphinx-autobuild` with `pip install sphinx-autobuild`.
