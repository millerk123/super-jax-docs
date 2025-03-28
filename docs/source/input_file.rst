Input file structure
====================

Input files for SUPER-JAX are written as yaml files.  Here we will go over the sections of the input file and the available parameters in each section.  The available sections of the input file are listed below:

* `mlflow`_ (optional)
* `grid`_
* `medium`_
* `laser`_
* `save`_

.. note::

   Floats given in scientific notation in a yaml file must be written with ``e+`` or ``e-``, e.g., ``1.2e+3`` or ``4.5e-6``.

.. _mlflow:

``mlflow`` (optional)
---------------------

If this section is present, then the output will be saved into a folder called ``mlruns``, and the data will have to be accessed using something like the ``mlflow ui`` command.  If you wish to avoid using MLflow, simply omit this section.

.. note::

   If you are running an "experiment" instead of just a single simulation, i.e., using the automatic differentiation capability to optimize certain parameters through gradient descent, then you will have to utilize the ``mlflow`` section.

Available parameters: **experiment**\ , **run**

   **experiment** : str
      Name of the experiment for this simulation.

   **run** : str
      Name of the run for this simulation.

``grid``
--------

The grid section determines the speed of the moving frame, as well as the number of points in :math:`t`\ , :math:`z`\ , and :math:`r`\ .

Available parameters: **vf**\ =\ *None*\ , **lambda0**\ , **tmin**\ , **tmax**\ , **nt**\ , **xmin**\ , **xmax**\ , **nx**\ , **max_steps**\ =\ *8192*\ , **ell**\ =\ *0*

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
----------

The medium section specifies the gas that any radiation will propagate through.

Available parameters: **atomdensity**\ =\ *None*\ , **atom**\

   **atomdensity** : float, optional
      The density of atoms specified by the **atom** parameter.  If **atomdensity** is left unspecified, the density will be set to standard temperature and pressure for the given **atom**\ .

   **atom** : str
      The atom to use for the medium.  Currently accepted values are *"hydrogen"*\ , *"helium"*\ , and *"argon"*\ .

.. note::

   The ``vg0`` parameter that is stored into the ``medium`` section of the dictionary in post-processing is the group velocity of the frequency specified by ``grid.lambda0``, which will be the frame velocity if ``grid.vf`` is left unspecified.

``laser``
---------

The laser section sets the physics to include for laser pulse propagation, spatial and temporal filters to use on the domain (crucial for stability), and any number of laser pulses to inject.

Available parameters: **bound_nonlinearity**\ , **ionization**\ , **filt_t_size**\ , **filt_r_size**\ , **filt_omega_size**\ , **filt_k_size**\ , **filt_angle**\ , **filt_omega0**\ , **vmap**\ =\ *True*\ , **pulses**

   **bound_nonlinearity** : bool
      Whether or not propagation includes the nonlinear response from bound electrons, i.e., the optical Kerr effect.  This term often leads to self-focusing of an intense laser pulse.  For more information, see Section 2.4.1 of the practitioner's guide.\ [1]_

   **ionization** : bool
      Whether or not propagation includes ionization of the gas (just the first level).  This term often leads to defocusing of an intense laser pulse in an ionization front.  For more information, see Section 2.4.4 of the practitioner's guide.\ [1]_

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
      The pulses section consists of numbers (beginning at 0) written as strings, one for each pulse that is desired in the simulation.  Each laser pulse must have a **type** parameter.  For example, if you want two pulses, the pulse section could look like

      .. code-block:: yaml

            pulses:
               "0":
                  type: "standard"
                  ...
               "1":
                  type: "ideal flying focus"
                  ...

      The **type** parameter must take on one of the below allowed values:

      * *"standard"*\ : A Gaussian pulse in the paraxial approximation, initialized in the far field.
      * *"ideal flying focus"*\ : Ideal representation of the flying focus.\ [2]_
      * *"axi-echelon flying focus"*\ : Axiparabola--echelon (or "ultrafast") flying focus.\ [3]_
      * *"axicon-echelon flying focus"*\ : Axicon--echelon flying focus.
      * *"sag"*\ : A Gaussian pulse in the paraxial approximation that is initialized in the near field and then focused by an optic with a custom sag function.
      * *"plasma lens"*\ : Hacked-together way to import a pulse profile and focus it with a plasma lens (hey, at least I'm honest).
      * *"custom"*\ : Pulse initialization using a custom-defined function, only for use when doing machine-learned optimization.

      See the sections below for more information on the parameters required for each pulse type.

Standard pulse
**************

The standard pulse is selected by setting **type** to "standard".  This type of pulse is initialized completely in the far field.  A Gaussian pulse first is initialized at focus, where the temporal profile can have a custom power **tpow** to make a super-Gaussain profile.  The pulse is then transformed to :math:`\omega`\ --\ :math:`k` space and propagated to the beginning of the simulation assuming vacuum propagation.  The available parameters for the standard pulse type are listed below.

Available parameters: **lambda0**\ , **I0**\ =\ *None*\ , **ene**\ =\ *None*\ , **tcent**\ , **tpulse**\ , **tpow**\ , **wf**\ , **zf**\ , **phase**

   **lambda0** : float
      The wavelength (m) of the pulse.

   **I0** : float, optional
      The peak intensity (W/cm\ :sup:`2`\ ) of the pulse *at focus*\ .

   **ene** : float, optional
      If the **I0** parameter is left unspecified, then the **ene** parameter is used to set the pulse amplitude.  It specifies the energy (J) of the pulse.  Note that either **I0** or **ene** must be specified.

   **tcent** : float
      Temporal center of the pulse (s).  This value should normally be within the limits of **tmin** and **tmax** from the `grid`_ section.

   **tpulse** : float
      Pulse duration (s).  The field profile is proportional to :math:`\exp\{-[(t-t_\mathrm{cent})/t_\mathrm{pulse}]^{t_\mathrm{pow}}\}`\ .  Therefore, when **tpow** is 2 then the full width at half maximum of the intensity is :math:`\mathrm{FWHM} = \sqrt{2\ln 2} t_\mathrm{pulse}`\ .

   **tpow** : float
      Power for the temporal profile of the field, which is proportional to :math:`\exp\{-[(t-t_\mathrm{cent})/t_\mathrm{pulse}]^{t_\mathrm{pow}}\}`\ .

   **wf** : float
      Spot size at focus (m).  The field profile is proportional to :math:`\exp[-(r/w_\mathrm{f})^2]` at the focus.

   **zf** : float
      The focal position (m) in :math:`z`\ .

   **phase** : float
      Phase constant (degrees) added to the field profile.


Ideal flying-focus pulse
************************

The ideal flying-focus pulse is selected by setting **type** to "ideal flying focus", and can be described mathematically by performing a Lorentz transformation on the fields of a multipole source.\ [2]_  In addition to all the parameters available for a `standard pulse`_\ , the parameters below are also available.

Available parameters: **vI**\ , **f0**\ , **nr_lens**\ , **rmaxf_lens**, **rpow**\ =\ *2*

   **vI** : float
      The speed of the focus (in units of the speed of light).

   **f0** : float
      Nominal focal length (m) of the focusing optic.

   **nr_lens** : int
      Number of grid points in the lens plane.

   **rmaxf_lens** : float
      Factor (that multiplies ``w0`` of the pulse at the lens plane) to determine ``rmax`` of the lens, i.e., ``rmax_lens = rmaxf_lens * w0``.

   **rpow** : float, optional
      Power for the radial profile of the field at the lens, which is proportional to :math:`\exp[-(r_\mathrm{lens}/w_0)^{r_\mathrm{pow}}]`\ .  The value of **rpow** defaults to 2.


Axiparabola--echelon flying-focus pulse
***************************************

The axiparabola--echelon flying-focus pulse is selected by setting **type** to "axi-echelon flying focus".  This pulse creates a flying focus (with focal velocity in the neighborhood of the speed of light) using a combination of an axiparabola and an echelon.\ [3]_   In addition to all the parameters available for a `standard pulse`_\ , the parameters below are also available.

Available parameters: **vI**\ , **f0**\ , **nr_lens**\ , **rmaxf_lens**, **rpow**\ =\ *2*, **echelon**\ , **Rap** \, **Lap**\ , **lambdaD**\ , **nlambfact**\ , **nr_sag**, **Rmin**\ =\ *None*

   **vI** : float
      The speed of the focus (in units of the speed of light).

   **f0** : float
      Nominal focal length (m) of the focusing optic.

   **nr_lens** : int
      Number of grid points in the lens plane.

   **rmaxf_lens** : float
      Factor (that multiplies ``w0`` of the pulse at the lens plane) to determine ``rmax`` of the lens, i.e., ``rmax_lens = rmaxf_lens * w0``.

   **rpow** : float, optional
      Power for the radial profile of the field at the lens, which is proportional to :math:`\exp[-(r_\mathrm{lens}/w_0)^{r_\mathrm{pow}}]`\ .  The value of **rpow** defaults to 2.

   **echelon** : bool
      Whether or not to apply the echelon.

   **Rap** : float
      Radius (m) of the axiparabola.

   **Lap** : float
      Length (m) of the focal region.

   **lambdaD** : float
      Wavelength (m) for which the echelon is designed.

   **nlambfact** : int
      Number of half wavelengths of **lambdaD** per echelon step.

   **nr_sag** : float
      Number of points in :math:`r` for calculating the sag function (recommended to be bewteen :math:`10^4`\ --\ :math:`10^6`\ ).

   **Rmin** : float, optional
      The inner radius of the optical assembly, inside which the beam is apodized (blocked).  If left unspecified, the full aperture is used.

.. note::

   The **I0** (or **ene**\ ) parameter for this pulse refers to the maximum intensity (or energy) of the pulse at the start of the focal region.  If the **Rmin** parameter is used, this can drastically affect the intensity at the beginning of the focal region, so using **ene** in this case would be more reliable.


Axicon--echelon flying-focus pulse
**********************************

The axicon--echelon flying-focus pulse is selected by setting **type** to "axicon-echelon flying focus".  This pulse creates a flying focus (with focal velocity in the neighborhood of the speed of light) using a combination of an axicon\ [4]_ and an echelon.  The difference between the axiparabola and axicon is in the expression for the focal length:

* Axiparabola: :math:`f(r) = f_0 + L_\mathrm{ap} (r/R_\mathrm{ap})^2`.
* Axicon: :math:`f(r) = f_0 + L_\mathrm{ap} (r^2 - R_\mathrm{min}^2) / (R_\mathrm{ap}^2 - R_\mathrm{min}^2)`.

All of the parameters for the axicon--echelon flying-focus pulse are the same as for the `axiparabola--echelon flying-focus pulse`_\ , except that the **Rmin** parameter is required.


Sag pulse
*********

The sag pulse initializes a Gaussian pulse in the paraxial approximation at the lens plane, then uses the sag function corresponding to an ideal lens with focal length **f0** to focus the pulse into the far field.  In addition to all the parameters available for a `standard pulse`_\ , the parameters below are also available.

Available parameters: **f0**\ , **nr_lens**\ , **rmaxf_lens**

   **f0** : float
      Nominal focal length (m) of the focusing optic.

   **nr_lens** : int
      Number of grid points in the lens plane.

   **rmaxf_lens** : float
      Factor (that multiplies ``w0`` of the pulse at the lens plane) to determine ``rmax`` of the lens, i.e., ``rmax_lens = rmaxf_lens * w0``.


Plasma lens pulse
*****************

The plasma lens pulse is not really intended for production use, but it is documented here anyway.  This type of pulse extends the basic functionality of the `sag pulse`_ with the options to (i) load in an initial pulse profile that uses real field quantities from OSIRIS and (2) to focus using a plasma lens instead of an ideal lens.  In addition to all the parameters available for a `sag pulse`_\ , the parameters below are also available.

Available parameters: **file**\ =\ *None*\ , **shape1**\ =\ *None*\ , **shape2**\ =\ *None*\ , **file_index**\ =\ *None*\ , **plasma_lens**

   **file** : str, optional
      The name of a numpy ``.npz`` file that contains the real field data on a grid of :math:`r` and :math:`z`\ .

   **shape1** : int, optional
      The number of zero-valued time points to add to the left of the data in **file**\ .

   **shape2** : int, optional
      The number of zero-valued time points to add to the right of the data in **file**\ .

   **file_index** : int, optional
      The radial index along which to search for the maximum frequency of the field (\ :math:`\omega_0` below).  Defaults to 0.

   **plasma_lens** : bool
      Whether to focus by a plasma lens optic (True) or an ideal lens (False), as with the `sag pulse`_\ .  The difference between the two focusing optics is that an ideal lens applies a phase of :math:`\exp\{i \omega r_\mathrm{lens}^2 / [c (2f_0 - 2s)] \}`\ , where :math:`s` is the sag function.  A plasma lens applies a phase of :math:`\exp[i \omega r_\mathrm{lens}^2 / (2cf_0) - 2 i \omega_0^2 s / (c\omega) ]`\ , where :math:`\omega_0` is the central frequency of the pulse.


Custom pulse
************

The custom pulse is for use only when doing machine-learned optimization problems.  It allows for the user to completely specify the function (often related to one of the other pulse initialization methods) to use for initializing the laser pulse.


``save``
---------

The save section determines the data that is saved and plotted.

Available parameters: **lineskip**\ , **fullskip**\ , **directory**\ , **plot_steps**\ =\ *False*\ , **display**\ =\ *True*\ , **dpi**\ =\ *300*\ , **save_sol**\ =\ *True*\ , **envelope**\ =\ *False*\ , **disable_output**\ =\ *False*\ , **upload**\ =\ *False*

   **lineskip** : int
      The number of :math:`z` points to skip when writing lineout quantities (see diagnostics for more information).

   **fullskip** : int
      The number of :math:`z` points to skip when writing full-grid quantities (see diagnostics for more information).  This value should normally be greater than **lineskip** to save on disk space, memory usage, and output time.

   **directory** : str
      The directory (will be created) to store output.  If the `mlflow`_ section is present, then this parameter is optional.  If **directory** is specified and the `mlflow`_ section is also present, then the artifacts will be copied to **directory** before being logged with MLflow.

   **plot_steps** : bool, optional
      Whether or not to plot the full-grid electric field for each step saved (both as a function of :math:`r` and :math:`t` as well as :math:`k` and :math:`\omega`\ ).  Defaults to False.

   **display** : bool, optional
      Whether to display (True) or close (False) any plots made after the simulation finishes.  Defaults to True.

   **dpi** : int, optional
      The dpi used for saving figures.  Defaults to 300.

   **save_sol** : bool, optional
      Whether or not to save the simulation output data, either as an MLflow artifact if the `mlflow`_ section is present, or as xarray data.  Defaults to True.

   **envelope** : bool, optional
      Whether to plot the envelope (True) or real values (False) of the electric field.  Defaults to False.

   **disable_output** : bool, optional
      Completely disable all pre- and post-processing routines, including plotting and saving the data.  Defaults to False.

   **upload** : bool, optional
      Upload the data to a remote MLflow server.  Defaults to False.




.. rubric:: References

.. [1] A\. Couairon, *et al*\ ., `"Practitioner’s guide to laser pulse propagation models and simulation," <https://doi.org/10.1140/epjst/e2011-01503-3>`_ *Eur. Phys. J.: Spec. Top.* **199**\ (1), 5-76 (2011).

.. [2] D\. Ramsey, *et al*\ ., `"Exact solutions for the electromagnetic fields of a flying focus," <https://doi.org/10.1103/PhysRevA.107.013513>`_ *Phys. Rev. A* **107**\ (1), 013513 (2023).

.. [3] M\. V. Ambat, *et al*\ ., `"Programmable-trajectory ultrafast flying focus pulses," <https://doi.org/10.1364/OE.499839>`_ *Opt. Express* **31**\ (19), 31354 (2023).

.. [4] A\. T. Friberg, `"Stationary-phase analysis of generalized axicons," <https://doi.org/10.1364/JOSAA.13.000743>`_ *J. Opt. Soc. Am. A* **13**\ (4), 743 (1996).
