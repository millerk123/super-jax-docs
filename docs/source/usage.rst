Usage
=====

The SUPER-JAX repository is written in Python using the `JAX <https://github.com/jax-ml/jax>`_ library, which allows for quick execution of just-in-time compiled code on either CPUs or GPUs.  See below for how to get started.

.. _installation:

Installation
------------

To use SUPER-JAX, create a custom conda environment for running on CPUs by executing

.. code-block:: console

   conda env create -f env.yaml

or for running on GPUs by executing

.. code-block:: console

   conda env create -f env_gpu.yaml

.. note::

   The ``env_gpu.yaml`` file was designed with Perlmutter in mind after loading the proper CUDA module as follows:

   .. code-block:: console

      module load cudnn/8.9.3_cuda12
