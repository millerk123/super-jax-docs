post_exp (optional)
==============

The ``post_exp`` section can be used when performing machine learning with SUPER-JAX, referred to as the experiment mode.  This section is used to re-run a particular case from a previously conducted experiment, with the option to save more frequently, change any input deck parameters, etc.

Available parameters: **parent_experiment**\ , **i_parent_run**\ , **i_run**\ , **modify**

   **parent_experiment** : str
      Name of the experiment under which we want to look for a parent and child combo to re-run.  This can be different from the experiment name of the :doc:`input_file/mlflow` section, which will be the experiment name under which the current run will be saved.

   **i_parent_run** : int
      The index of the parent run to select from the experiment.  The parent runs are sorted with the most recent first, so setting **i_parent_run** to 1 will select the second-to-last parent run.

   **i_run** : int
      The index of the child run to select from the experiment.  The child runs are sorted with the most recent first, so setting **i_run** to 1 will select the second-to-last child run.

   **modify** : str []
      Array of strings that gives quantities from the current input deck to overwrite in the saved config file of the specified child run.  These should be specified with the character "." denoting layers in the input file, e.g., setting **modify** to ``["laser.pulses.0.type", "grid.nt"]``\ .  Note that the *"mlflow"* and *"save"* sections of the current input file automatically overwrite the corresponding config file sections.
