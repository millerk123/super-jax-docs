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

Available parameters: **experiment**\ , **run**

   **experiment** : str
      Name of the experiment for this simulation.

   **run** : str
      Name of the run for this simulation.

``grid``
-----

The grid section determines the speed of the moving frame, as well as the number of points in :math:`t`\ , :math:`z`\ , and :math:`r`\ .

Available parameters: **vf**\ *=None*\ , **lambda0**\ , **tmin**\ , **tmax**\ , **nt**\ , **xmin**\ , **xmax**\ , **nx**\ , **max_steps**\ *=8192*\ , **ell**\ *=0*

   **vf** : float, optional
      The speed of the moving frame (in units of the speed of light).  If this parameter is left blank (recommended), the moving frame will be set to move at the group velocity of the wavelength specified by **lambda0** in the given medium.  This is ideal for stability, and the calculated value of **vf** can later be retrieved from the output data.

   **lambda0** : float
      The wavelength (m) that specifies the default frequency for calculation of the moving-frame velocity and for filtering.  This should ideally be set to the main frequency of the laser light.

   **tmin** : float
      The minimum time (s) used for the time grid.

   **tmax** : float
      The maximum time (s) used for the time grid.

   **nt** : int
      The number of points used for the time grid.  Since many FFTs will be taken in this dimension, the algorithm will be fastest if this is a power of 2.  Otherwise, a number of the form :math:`2^a 3^b 5^c 7^d 11^e 13^f`\ , where :math:`e+f` is either 0 or 1, will also be `reasonably efficient <https://www.fftw.org/fftw2_doc/fftw_3.html>`_\ .

   **xmin** : floats [zmin, rmin]
      The minimum spatial coordinates (m) in :math:`z` and :math:`r`\ , respectively.  The value of **rmin** should be set to 0.

   **xmax** : floats [zmax, rmax]
      The maximum spatial coordinates (m) in :math:`z` and :math:`r`\ , respectively.

   **nx** : ints [nz, nr]
      The number of points used in :math:`z` and :math:`r`\ , respectively.

   **max_steps** : int, default: 8192
      The Diffrax ``diffeqsolve`` function requires the **max_steps** parameter, which is the maximum number of steps to take before quitting the computation unconditionally.  If **nx[0]**, i.e., the **nz** parameter, is set to be larger than 8192, then **max_steps** should also be increased accordingly.

   **ell** : int, default: 0
      The azimuthal index :math:`\ell` of the simulation, which can take on values of either 0 or 1.  If set to 0, the simulation corresponds to a cylindrically symmetric pulse.  If set to 1, a laser pulse initialized like a Laguerre--Gaussian mode with :math:`\ell = 1` will propagate properly.  Setting **ell** to 1, however, is an experimental mode, and most types of laser pulses will not work well.

``medium``
-----

The medium section specifies the gas that any radiation will propagate through.

Available parameters: **atomdensity**\ *=None*\ , **atom**\

   **atomdensity** : float, optional
      The density of atoms specified by the **atom** parameter.  If **atomdensity** is left unspecified, the density will be set to standard temperature and pressure for the given **atom**\ .

   **atom** : str
      The atom to use for the medium.  Currently accepted values are *"hydrogen"*\ , *"helium"*\ , and *"argon"*\ .

.. note::

   The ``vg0`` parameter that is stored into the ``medium`` section of the dictionary in post-processing is the group velocity of the frequency specified by ``grid.lambda0``, which will be the frame velocity if ``grid.vf`` is left unspecified.

``laser``
-----

The laser section sets the physics to include for laser pulse propagation, spatial and temporal filters to use on the domain (crucial for stability), and any number of laser pulses to inject.

Available parameters: **bound_nonlinearity**\ , **ionization**\ , **filt_t_size**\ , **filt_r_size**\ , **filt_omega_size**\ , **filt_k_size**\ , **filt_angle**\ , **filt_omega0**\ , **vmap**\ *=True*\ , **pulses**

   **bound_nonlinearity** : bool
      Whether or not propagation includes the nonlinear response from bound electrons, i.e., the optical Kerr effect.  This term often leads to self-focusing of an intense laser pulse.  For more information, see Section 2.4.1 of the practitioner's guide\ [1]_\ .

   **ionization** : bool
      Whether or not propagation includes ionization of the gas (just the first level).  This term often leads to defocusing of an intense laser pulse in an ionization front.  For more information, see Section 2.4.4 of the practitioner's guide\ [1]_\ .

   **filt_t_size** : float
      The fraction of the total :math:`t` grid over which to taper the response to zero (using a cosine function) at the left and right edges.  For example, a value of 0.05 (recommended) will leave the middle 90% of the temporal grid untouched, but taper 5% on the left and 5% on the right to zero.

   **filt_r_size** : float
      The fraction of the total :math:`r` grid over which to taper the response to zero (using a cosine function) at the top edge.  For example, a value of 0.1 (recommended) will leave the central 90% of the radial grid untouched, but taper 10% on the top to zero.

   **filt_omega_size** : float
      The fraction of the positive :math:`\omega` grid to zero out at the right, high-frequency edge.  All negative frequencies are set to zero at each iteration automatically.  The **filt_omega_size** parameter allows for certain large, positive frequencies to be zeroed out as well.  For example, a value of 0.1 will leave frequencies from 0 to 90% of the Nyquist frequency untouched, but zero out frequencies above 90% of the Nyquist frequency.

   **filt_k_size** : float
      The fraction of the positive :math:`k` grid to zero out at the high-\ :math:`k` edge (note that :math:`k` really refers to the perpendicular wavenumber, :math:`k_\perp`\ ).  For example, a value of 0.1 will leave :math:`k` from 0 to 90% of the maximum :math:`k` untouched, but zero out :math:`k` values above 90% of the maximum :math:`k`\ .

   **filt_angle** : float
      Waves with angle larger than **filt_angle** (in degrees) from the :math:`z`\ -axis are zeroed out during propagation.  The angle is determined in :math:`k`\ -space.

   **filt_omega0** : float
      Frequencies below :math:`\omega_0`\ are zeroed out, where :math:`\omega_0` is determined by the value of **lambda0** in the `grid`_ section.

   **vmap** : bool, default: True
      Whether to use a `vmap <https://docs.jax.dev/en/latest/_autosummary/jax.vmap.html>`_ (True) or a `lax loop <https://docs.jax.dev/en/latest/_autosummary/jax.lax.map.html>`_ (False) when computing the fresnel integral at the lens in the near field.  Using a vmap is typically much faster, but it can consume more memory than a lax loop.

   **pulses** : section
      The pulses section consists of numbers (beginning at 0) written as strings, one for each pulse that is desired in the simulation.  For example, if you want to pulses, the pulse section would look like
      .. code-block:: yaml

      pulses:
         "0":
            type: "standard"
            ...
         "1":
            type: "sag"
            ...

      See the below section on **pulses** for more information.

``pulses``
*****

   Each laser pulse must have a **type** parameter, which must take on one of the below allowed values:
   * *"standard"*\ : Stuff
   * *"ideal flying focus"*\ : Stuff
   * *"axi-echelon flying focus"* or *"axicon-echelon flying focus"*\ : Stuff
   * *"sag"*\ : Stuff
   * *"plasma lens"*\ : Stuff
   * *"custom"*\ : Stuff

Standard pulse
#####

The standard pulse is selected by setting **type** to "standard".  This type of pulse is initialized completely in the far field.  A Gaussian pulse first is initialized at focus, where the temporal profile can have a custom power **tpow** to make a super-Gaussain profile.  The pulse is then transformed to :math:`\omega`\ --\ :math:`k` space and propagated to the beginning of the simulation assuming vacuum propagation.  The available parameters for the standard pulse type are listed below.

Available parameters: **bound_nonlinearity**\ , **ionization**\ , **filt_t_size**\ , **filt_r_size**\ , **filt_omega_size**\ , **filt_k_size**\ , **filt_angle**\ , **filt_omega0**\ , **vmap**\ *=True*\ , **pulses**

   **bound_nonlinearity** : bool
      Whether or not propagation includes the nonlinear response from bound electrons, i.e., the optical Kerr effect.  This term often leads to self-focusing of an intense laser pulse.  For more information, see Section 2.4.1 of the practitioner's guide\ [1]_\ .




References
-----

.. [1] A\. Couairon, E. Brambilla, T. Corti, D. Majus, O. de J. Ramírez-Góngora, and M. Kolesik, `"Practitioner’s guide to laser pulse propagation models and simulation," <https://doi.org/10.1140/epjst/e2011-01503-3>`_ *Eur. Phys. J.: Spec. Top.* **199**\ (1), 5-76 (2011).
