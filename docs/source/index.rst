Welcome to the SUPER-JAX documentation!
=======================================

**SUPER** (\ **S**\ imulation of the **U**\ nidirectional pulse **P**\ ropagation **E**\ quation at **R**\ ochester) is a software package built in Fortran for simulating the Unidirectional Pulse Propagation Equation (UPPE).\ [1]_\ :sup:`--`\ [3]_  This code was adapted to Python with JAX integration by Kyle G. Miller and Archis S. Joglekar from MATLAB code written by John P. Palastro.

Check out the :doc:`usage` section for further information, including
how to :ref:`install<installation>` the project.

.. note::

   This project is under active development.

Features
--------

✅ Automatic differentiation

✅ GPU-ready with `JAX <https://github.com/jax-ml/jax>`_

✅ Integration with `mlflow <https://github.com/mlflow/mlflow>`_

Contents
--------

.. toctree::

   usage
   input_file
   api

.. rubric:: References

.. [1] M\. Kolesik, J.V. Moloney, and M. Mlejnek, `"Unidirectional Optical Pulse Propagation Equation," <https://doi.org/10.1103/PhysRevLett.89.283902>`_ *Phys. Rev. Lett.* **89**\ (28), 283902 (2002).

.. [2] M\. Kolesik and J.V. Moloney, `"Nonlinear optical pulse propagation simulation: From Maxwell’s to unidirectional equations," <https://doi.org/10.1103/PhysRevE.70.036604>`_ *Phys. Rev. E* **70**\ (3), 036604 (2004).

.. [3] A\. Couairon, *et al*\ ., `"Practitioner’s guide to laser pulse propagation models and simulation," <https://doi.org/10.1140/epjst/e2011-01503-3>`_ *Eur. Phys. J.: Spec. Top.* **199**\ (1), 5-76 (2011).