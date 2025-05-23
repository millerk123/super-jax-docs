exp (optional)
==============

The ``exp`` section is used when performing machine learning with SUPER-JAX, referred to as the experiment mode.  This section is used to specify parameters for the `OptaxSolver <https://jaxopt.github.io/stable/_autosummary/jaxopt.OptaxSolver.html>`_ (a part of `JAXopt <https://jaxopt.github.io/stable/index.html>`_ that extends `Optax <https://optax.readthedocs.io/en/latest/index.html>`_\ ), pulse initialization in experiments, parameters relating to the loss function, and the parameters to be optimized (lens shape, pulse characteristics, etc.).  This section is a bit fluid, and users could easily add parameters as they see fit.

.. note::

   Since the experiment mode uses a custom pulse initialization, the only parameters that are used from the regular ``pulse`` section are **lambda0**\ , **tcent**\ , **tpulse**\ , **tpow**\ , **wf**\ , **phase**\ , **f0**\ , **nr_lens**\ , **rmaxf_lens**\ , **rpow**\ , **rpow_2**\ , and **w0_2**\ .  This means that the ignored parameters include **I0**\ , **ene**\ , **zf**\ , **vI**\ , and any extra parameters.

.. _nuances:

Nuances of optimization with JAX and Optax
------------------------------------------

Here we give a brief aside about how optimization is done using JAX and the Optax solvers, and some tricks that we have to play in order for things to play nicely.  For terminology, we will use "parameters" for the numbers that the optimizer is varying in order to minimize the loss function.

First, the optimizers like it when all parameters are of order unity.  Additionally, the **learning_rate** that is provided to the optimizer determines how rapdily the parameters can vary.  Since we wish to vary many different parameters, some of which are orders of magnitude larger/smaller than others, we have to make this work with the way the optimizers work.  For example, for an average problem we may want the coefficients for the 0th-, 2nd-, 4th-, and 6th-order Zernike polynomials to vary on the order of 0.1, 0.1, 0.005, and 0.0001, respectively.  This can be allowed by working in log space, where the log of all of these numbers are of the same magnitude.  However, we also want to allow for the coefficients for the Zernike polynomials to be negative, something that cannot be accomplished in pure log space.

In order to get around these issues, we do work in log space, but we give each coefficient a corresponding positive and negative parameter.  The actual coefficient for the :math:`n^\mathrm{th}`\ -order Zernike polynomial is then computed as :math:`A_n = A_n^0 + 10^{A_n^+} - 10^{A_n^-}`\ , where :math:`A_n^0` is the default coefficient for a given lens, and :math:`A_n^+` and :math:`A_n^-` are the positive and negative parameters in log space that contribute to :math:`A_n`\ .  We usually want to start the Zernike polynomials at their default values for a given problem, which, for the 0th-order Zernike polynomial, could be accomplished by setting :math:`A_n^+ = A_n^- = \log_{10} 0.1`\ .  This could be done in the input deck by setting components of **init_lens_coefs** to ``[[0.1, 0.1, ...], [0.1, 0.1, ...], ...]`` (see below).

Standard available parameters
-----------------------------

Based on the current structure of the optimization algorithm, these are standard parameters that can be used for all types of experimental runs.

Available parameters: **adjoint**\ , **learning_rate**\ , **n_opt**\ , **save_x**\ , **upload**\ , **bandwidth**\ , **filt_t_size_init**\ , **filt_r_size_init**\ , **ene**\ , **nr_coefs**\ , **window_len**\ , **lens_variation**\ , **init_lens_coefs**\ , **scale_high_zernikes**\ , **scale_lens_free**\ , **window_len_free**\ , **pulse_variation**\ , **optics**\ , **scale_echelon**

   **adjoint** : str
      Adjoint method for the diffrax solver, either *"recursive"* (recommended, faster, uses more memory) or *"backsolve"* (slower, uses less memory).

   **learning_rate** : float
      The learning rate to use with the Optax `Adam <https://optax.readthedocs.io/en/latest/api/optimizers.html#optax.adam>`_ optimizer (or whichever optimizer the user implements).  The optimizers work best if the loss function output is of order unity, and **learning_rate** is of order :math:`10^{-4}`\ --\ :math:`10^{-2}`\ .

   **n_opt** : int
      Number of simulations to conduct for the optimization.

   **save_x** : bool
      Whether or not to save the lineout data, lens data, and other data that is plotted.  This parameter is related to the **save_sol** parameter in the :doc:`input_file/save` section of the input file.  If only **save_sol** is turned on, then the full-grid and lineout data will be saved.  If only **save_x** is turned on, then the lineout data along with extra experiment-specific data will be saved.  If both are turned on, then all data will be saved.

   **upload** : bool
      Whether or not to upload the MLflow run to the server (hosted by Archis).

   **bandwidth** : float
      Fraction of the pulse frequency that governs how much the central frequency and duration can vary.

   **filt_t_size_init** : float
      Similar to the **filt_t_size_left** and **filt_t_size_right** parameters from the :doc:`input_file/laser` section, but this parameter applies to both left and right sides, and is only used to filter the initial pulse.  This is done so that the user can specify a more stringent filter at initialization.

   **filt_r_size_init** : float
      Similar to the **filt_r_size** parameter from the :doc:`input_file/laser` section, but this parameter is only used to filter the initial pulse.  This is done so that the user can specify a more stringent filter at initialization.

   **ene** : float
      The energy contained in the pulse at the lens.  Note that **I0** and **ene** in the :doc:`input_file/laser` section are ignored.

   **nr_coefs** : int
      Number of coefficients (i.e., discrete points) in :math:`r` owned by the *"free"* variation, the *"echelon"* optic, as well as the pulse variations of *"amplitude"*\ , *"frequency"*\ , and *"duration"*\ .  This number of points can be less than the **nr_lens** number in the :doc:`input_file/laser` section, since the coefficients will be interpolated onto the finer lens grid and then smoothed before use.

   **window_len** : int
      The window size that is specified in the `Hamming window <https://docs.jax.dev/en/latest/_autosummary/jax.numpy.hamming.html>`_ used to smooth the pulse variations of *"amplitude"*\ , *"frequency"*\ , and *"duration"*\ .

   **lens_variation** : strings [[]]
      A nested list of strings that specifies the allowed variation in the lens sag for different powers of frequency.  The length of the outer list determines how many powers of frequency are used, where the first element is :math:`(\omega/\omega_0 - 1)^0`\ , the second is :math:`(\omega/\omega_0 - 1)^1`\ , and so on.  The **init_lens_coefs** and **scale_lens_free** parameters must be of the same length.  The code has not been well tested for powers above 3 (list length 4).  Each item in the list can either be a list of variations or a single variation.  The current allowed variations in the lens are *"Zernike"* and *"free"*\ .  An example of the **lens_variation** parameter would then be ``["Zernike", ["Zernike", "free"]]``\ .

   **init_lens_coefs** : floats [[]]
      A nested list of floats that specifies the initial coefficients of Zernike polynomials for different powers of frequency.  This list must have the same length as **lens_variation** and **scale_lens_free**\ , where the length determines how many powers of frequency are used.  The code is written to utilize Zernike polynomials of order 0, 2, 4, and 6.  Since we use a positive and negative variation parameter for each order Zernike (as explained `above <#nuances>`_\ ), an item in the list should also be a list with values :math:`[A_0^+, A_0^-, A_2^+, A_2^-, A_4^+, A_4^-, A_6^+, A_6^-]`\ , where the subscript refers to the Zernike order and the superscript refers to whether the contribution is positive or negative.

      The Zernike coefficients are all initialized at their default values :math:`A_n^0` for a given focus, so it is usually prudent to set :math:`A_n^+ = A_n^-` such that the optimization starts from the expected default value.  This does not necessarily have to be the case, e.g., :math:`A_0^+ = 0.1` and :math:`A_0^- = 0.2` would initialize the 0th-order Zernike coefficient to be :math:`A_n = A_n^0 + 0.1 - 0.2 = A_n^0 - 0.1`\ .  The specified parameters also determine how quickly the coefficients will vary, and the variation should usually be much smaller for the 4th- and 6th-order Zernike polynomials.  For example, a recommended initialization for each element of **init_lens_coefs** is ``[0.1, 0.1, 0.1, 0.1, 0.005, 0.005, 0.0001, 0.0001]``\ .

   **scale_high_zernikes** : float
      This is a parameter which can scale the 4th- and 6th-order Zernike coefficients more rapidly than the others.  This parameter may not be so necessary since we now work in log space.  It should be set to 1 if no scaling is desired, and it could be set to something like 10 if faster scaling is desired.

   **scale_lens_free** : floats []
      A list of floats that determines how quickly the free lens function varies (as a multiple of :math:`\lambda_0`\ ) for different powers of frequency.  This list must have the same length as **lens_variation** and **init_lens_coefs**\ , where the length determines how many powers of frequency are used.  A good initial value for each element might be 8.0, but this will be problem-dependent.

   **window_len_free** : int
      The window size that is specified in the `Hamming window <https://docs.jax.dev/en/latest/_autosummary/jax.numpy.hamming.html>`_ used to smooth the *"echelon"* and *"free"* variations.

   **pulse_variation** : strings []
      List of strings specifying the varying pulse parameters.  Allowed values are *"amplitude"*, *"frequency"*, and *"duration"*.

   **optics** : strings []
      List of additional optics to put in the assembly.  Currently, the only available option is *"echelon"*.

   **scale_echelon** : int
      Determines how quickly the echelon shape varies (as a multiple of :math:`\lambda_0`\ ).  A reasonable starting value might be 5.

Custom parameters
-----------------

In order to implement a custom experiment and optimization algorithm, the user will likely desire to specify a number of extra inputs in the input deck.  The convention is to specify these in the ``exp`` section of the input deck, then use them in the corresponding ``run_exp_*.py`` file for the given experiment.  Below, we list parameters that are used in the ``run_exp_axi_echelon.py`` experiment as an example of what a user could implement.

Example parameters: **r_count**\ , **z_count**\ , **t_count**\ , **filename**\ , **vI**\ , **E0**\ , **FWHM**\ , **compare_data**

   **r_count** : float
      TBD.

   **z_count** : floats [zmin, zmax]
      TBD.

   **t_count** : floats [tmin, tmax]
      TBD.

   **filename** : str
      TBD.

   **vI** : float
      Desired focal velocity of the pulse.

   **E0** : float
      Desired on-axis electric field value.

   **FWHM** : float
      TBD.

   **compare_data** : bool
      TBD.
