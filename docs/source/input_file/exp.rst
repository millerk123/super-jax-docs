exp (optional)
==============

The ``exp`` section is used when performing machine learning with SUPER-JAX, referred to as the experiment mode.  This section is used to specify parameters for the `OptaxSolver <https://jaxopt.github.io/stable/_autosummary/jaxopt.OptaxSolver.html>`_ (a part of `JAXopt <https://jaxopt.github.io/stable/index.html>`_ that extends `Optax <https://optax.readthedocs.io/en/latest/index.html>`_\ ), pulse initialization in experiments, parameters relating to the loss function, and the parameters to be optimized (lens shape, pulse characteristics, etc.).  This section is a bit fluid, and users could easily add parameters as they see fit.

Available parameters: **adjoint**\ , **learning_rate**\ , **n_opt**\ , **save_x**\ , **upload**\ , **bandwidth**\ , **filt_t_size_init**\ , **filt_r_size_init**\ , **ene**\ , **nr_coefs**\ , **window_len**\ , **r_count**\ , **z_count**\ , **t_count**\ , **filename**\ , **vI**\ , **E0**\ , **FWHM**\ , **compare_data**\ , **lens_variation**\ , **scale_high_zernikes**\ , **init_lens_coefs**\ , **init_lens_free**\ , **pulse_variation**

   **adjoint** : str
      Adjoint method for the diffrax solver, either *"recursive"* (recommended, faster, uses more memory) or *"backsolve"* (slower, uses less memory).
