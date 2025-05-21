``save``
========

The save section determines the data that is saved and plotted.

Available parameters: **lineskip**\ , **fullskip**\ , **directory**\ , **display**\ =\ *1*\ , **plot_steps**\ =\ *False*\ , **display**\ =\ *True*\ , **dpi**\ =\ *300*\ , **save_sol**\ =\ *True*\ , **envelope**\ =\ *False*\ , **disable_output**\ =\ *False*\ , **upload**\ =\ *False*

   **lineskip** : int
      The number of :math:`z` points to skip when writing lineout quantities (see diagnostics for more information).

   **fullskip** : int
      The number of :math:`z` points to skip when writing full-grid quantities (see diagnostics for more information).  This value should normally be greater than **lineskip** to save on disk space, memory usage, and output time.

   **directory** : str
      The directory (will be created) to store output.  If the :doc:`input_file/mlflow` section is present, then this parameter is optional.  If **directory** is specified and the :doc:`input_file/mlflow` section is also present, then the artifacts will be copied to **directory** before being logged with MLflow.

   **n_loops** : int, optional
      This parameter should only be set if the GPU is running out of memory when trying to store the full grid data for the electric field and electron density.  It specifies the number of ``diffeqsolve`` loops to use over the full :math:`z` distance.  The default value is 1, which is ideal for speed and should be used if not running out of memory.  When **n_loops** is set to greater than 1, then the ``diffeqsolve`` is jitted and executed **n_loops** times.  This allows for the storage of smaller arrays on the GPU, which are accumulated and written out on the CPU, where memory is plentiful.

   **plot_steps** : bool, optional
      Whether or not to plot the full-grid electric field for each step saved (both as a function of :math:`r` and :math:`t` as well as :math:`k` and :math:`\omega`\ ).  Defaults to False.

   **display** : bool, optional
      Whether to display (True) or close (False) any plots made after the simulation finishes.  Defaults to True.

   **dpi** : int, optional
      The dpi used for saving figures.  Defaults to 300.

   **save_sol** : bool, optional
      Whether or not to save the simulation output data, either as an MLflow artifact if the :doc:`input_file/mlflow` section is present, or as xarray data.  Defaults to True.

   **envelope** : bool, optional
      Whether to plot the envelope (True) or real values (False) of the electric field.  Defaults to False.

   **disable_output** : bool, optional
      Completely disable all pre- and post-processing routines, including plotting and saving the data.  Defaults to False.

   **upload** : bool, optional
      Upload the data to a remote MLflow server.  Defaults to False.
