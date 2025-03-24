Usage
=====

The SUPER-JAX repository is written in Python using the `JAX <https://github.com/jax-ml/jax>`_ library, which allows for quick execution of just-in-time compiled code on either CPUs or GPUs.  See below for how to get started.

.. _installation:

Installation
------------

To be able to run SUPER-JAX, first create a custom conda environment for running on CPUs by executing

.. code-block:: console

   conda env create -f env.yaml

or for running on GPUs by executing

.. code-block:: console

   conda env create -f env_gpu.yaml

This will create a conda environment named either ``super_jax`` or ``super_jax_1`` for running on CPUs and GPUs, respectively.  After activating this environment, you should be ready to run SUPER-JAX.

.. note::

   The ``env_gpu.yaml`` file was designed with Perlmutter in mind after loading the proper CUDA module as follows:

   .. code-block:: console

      module load cudnn/8.9.3_cuda12

.. _usage:

Basic usage
------------

The most standard way to run SUPER-JAX is to perform a single simulation based on an input file.  This is done by executing

.. code-block:: console

   python run.py "path/to/input_file.yaml"

If no argument is supplied to the ``python run.py`` command, then the default file used is ``"configs/default.yaml"``.  A simulation will be performed based on the parameters in the input file, the output will be saved, and then the code will terminate.
