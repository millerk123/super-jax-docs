mlflow (optional)
=================

If the ``mlflow`` section is present, then the output will be saved into a folder called ``mlruns``, and the data will have to be accessed using something like the ``mlflow ui`` command.  If you wish to avoid using MLflow, simply omit this section.

.. note::

   If you are running an "experiment" instead of just a single simulation, i.e., using the automatic differentiation capability to optimize certain parameters through gradient descent, then you will have to utilize the ``mlflow`` section.

Available parameters: **experiment**\ , **run**

   **experiment** : str
      Name of the experiment for this simulation.

   **run** : str
      Name of the run for this simulation.
