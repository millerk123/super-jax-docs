Input file structure
=====

Input files for SUPER-JAX are written as yaml files.  Here we will go over the sections of the input file and the available parameters in each section.  The available sections of the input file are listed below:

* `mlflow`_
* `grid`_

.. note::

   Floats given in scientific notation in a yaml file must be written with ``e+`` or ``e-``, e.g., ``1.2e+3`` or ``4.5e-6``.

.. _mlflow:

``mlflow`` (optional)
-----

If this section is present, then the output will be saved into a folder called ``mlruns``, and the data will have to be accessed using something like the ``mlflow ui`` command.  If you wish to avoid using ``mlflow``, simply omit this section.

.. note::

   If you are running an "experiment" instead of just a single simulation, i.e., using the automatic differentiation capability to optimize certain parameters through gradient descent, then you will have to utilize the ``mlflow`` section.

Input parameters
*****

**experiment**\ , **run**

   **experiment : str**
      Name of the experiment for this simulation.

   **run : str**
      Name of the run for this simulation

``grid``
-----

Cool
