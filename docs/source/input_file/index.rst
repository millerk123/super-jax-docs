Input file structure
====================

Input files for SUPER-JAX are written as yaml files.  Here we will go over the sections of the input file and the available parameters in each section.  The available sections of the input file are listed below:

.. toctree::
   :maxdepth: 1
   :caption: Sections

   `mlflow` (optional) <input_file/mlflow>
   grid
   medium
   laser
   save

.. note::

   Floats given in scientific notation in a yaml file must be written with ``e+`` or ``e-``, e.g., ``1.2e+3`` or ``4.5e-6``.
