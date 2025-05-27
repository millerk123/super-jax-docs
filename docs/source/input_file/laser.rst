laser
=====

The ``laser`` section sets the physics to include for laser pulse propagation, spatial and temporal filters to use on the domain (crucial for stability), and any number of laser pulses to inject.

Available parameters: **bound_nonlinearity**\ , **ionization**\ , **filt_t_size_left**\ =\ *0.1*\ , **filt_t_size_right**\ =\ *0.1*\ , **filt_r_size**\ =\ *0.1*\ , **filt_omega_high_min**\ =\ *0.9*\ , **filt_omega_high_max**\ =\ *1.0*\ , **filt_k_high_min**\ =\ *0.9*\ , **filt_k_high_max**\ =\ *1.0*\ , **filt_angle_min**\ =\ *θ_max*\ , **filt_angle_max**\ =\ *θ_max + 10*\ , **filt_omega_grid_min**\ =\ *1.0*\ , **filt_omega_grid_max**\ =\ *10.0*\ , **nz_filt_t**\ =\ *2*\ , **nz_filt_omega**\ =\ *2*\ , **solver**\ =\ *"direct"*\ , **vmap**\ =\ *True*\ , **pulses**

   **bound_nonlinearity** : bool
      Whether or not propagation includes the nonlinear response from bound electrons, i.e., the optical Kerr effect.  This term often leads to self-focusing of an intense laser pulse.  For more information, see Section 2.4.1 of the practitioner's guide.\ [1]_

   **ionization** : bool
      Whether or not propagation includes ionization of the gas (just the first level).  This term often leads to defocusing of an intense laser pulse in an ionization front.  For more information, see Section 2.4.4 of the practitioner's guide.\ [1]_

   **filt_t_size_left** : float, optional
      The fraction of the total :math:`t` grid over which to taper the response to zero (using a cosine function) at the left edge.  For example, a value of 0.1 for **filt_t_size_left** and **filt_t_size_right** will leave the middle 80% of the temporal grid untouched, but taper 10% on the left and 10% on the right to zero.  Defaults to 0.1.

   **filt_t_size_right** : float, optional
      The same as **filt_t_size_left**\ , but for the right edge of the temporal domain.

   **filt_r_size** : float, optional
      The fraction of the total :math:`r` grid over which to taper the response to zero (using a cosine function) at the top edge.  For example, a value of 0.1 (recommended) will leave the central 90% of the radial grid untouched, but taper 10% on the top to zero.  Defaults to 0.1.

   **filt_omega_high_min** : float, optional
      The multiple of the Nyquist frequency, :math:`\omega_\mathrm{Ny} = \pi / \Delta t`\ , at which to begin filtering out high-frequency content.  A smooth :math:`\cos^2` filter is applied from **filt_omega_high_min** to **filt_omega_high_max**\ .  Defaults to 0.9.

   **filt_omega_high_max** : float, optional
      The multiple of the Nyquist frequency, :math:`\omega_\mathrm{Ny} = \pi / \Delta t`\ , above which to exclude high-frequency content.  A smooth :math:`\cos^2` filter is applied from **filt_omega_high_min** to **filt_omega_high_max**\ .  Defaults to 1.0.

   **filt_k_high_min** : float, optional
      The multiple of the Nyquist wavenumber at which to begin filtering out high-wavenumber content.  A smooth :math:`\cos^2` filter is applied from **filt_k_high_min** to **filt_k_high_max**\ .  Defaults to 0.9.

   **filt_k_high_max** : float, optional
      The multiple of the Nyquist wavenumber above which to exclude high-wavenumber content.  A smooth :math:`\cos^2` filter is applied from **filt_k_high_min** to **filt_k_high_max**\ .  Defaults to 1.0.

   **filt_angle_min** : float, optional
      The angle (degrees) from the :math:`z`\ -axis at which to begin filtering out spectral content.  A smooth :math:`\cos^2` filter is applied from **filt_angle_min** to **filt_angle_max**\ .  Defaults to :math:`\theta_\mathrm{max} = \tan^{-1}[(z_\mathrm{max} - z_\mathrm{min}) / r_\mathrm{max}]`, or the angle at which light originating from the axis at the first :math:`z` location will hit the radial boundary at the last :math:`z` location.

   **filt_angle_max** : float, optional
      The angle (degrees) from the :math:`z`\ -axis above which to exclude spectral content.  A smooth :math:`\cos^2` filter is applied from **filt_angle_min** to **filt_angle_max**\ .  Defaults to **filt_angle_min** + 10.

   **filt_omega_grid_min** : float, optional
      The multiple of the lowest frequency, :math:`\omega_\mathrm{grid} = 2\pi/(t_\mathrm{max} - t_\mathrm{min})`\ , below which to exclude low-frequency content.  A smooth :math:`\cos^2` filter is applied from **filt_omega_grid_min** to **filt_omega_grid_max**\ .  Defaults to 1.0.

   **filt_omega_grid_max** : float, optional
      The multiple of the lowest frequency, :math:`\omega_\mathrm{grid} = 2\pi/(t_\mathrm{max} - t_\mathrm{min})`\ , below which to begin filtering out low-frequency content.  A smooth :math:`\cos^2` filter is applied from **filt_omega_grid_min** to **filt_omega_grid_max**\ .  Defaults to 10.0.

   **nz_filt_t** : int, optional
      Apply the temporal filters every **nz_filt_t** steps in :math:`z`\ .  Filtering can be too aggressive when done at every :math:`z` step.  Defaults to 2.

   **nz_filt_omega** : int, optional
      Apply the spectral filters every **nz_filt_omega** steps in :math:`z`\ .  Filtering can be too aggressive when done at every :math:`z` step.  Defaults to 2.

   **solver** : str, optional
      Which solver to use for solving the UPPE.  The options include *"direct"* and *"rk4"*\ .  The *"direct"* solver employs an RK2-like method similar to Heun's method.  The *"rk4"* solver uses the standard RK4 solver, which can be ~67% more time intensive than the *"direct"* solver.  Defaults to *"direct"*\ .

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

.. _ideal flying focus pulse:

Ideal flying-focus pulse
************************

The ideal flying-focus pulse is selected by setting **type** to "ideal flying focus", and can be described mathematically by performing a Lorentz transformation on the fields of a multipole source.\ [2]_  In addition to all the parameters available for a `standard pulse`_\ , the parameters below are also available.

Available parameters: **vI**\ , **f0**\ , **nr_lens**\ , **rmaxf_lens**, **rpow**\ =\ *2*, **rpow_2**\ =\ *None*, **w0_2**\ =\ *None*

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

   **rpow_2** : float, optional
      A second power to also multiply in to the field at the lens, similar to what is done for **rpow**\ .  The value of **rpow_2** defaults to *None* and has no effect.  This parameter could be useful, for example, to simulate a Gaussian pulse profile incident on a lens with a hard radial cutoff.  In that case, the parameters **rpow** = 2 and **rpow_2** = 40 could be used.

   **w0_2** : float, optional
      The spot size in the exponential applied with the **rpow_2** parameter.  Must be specified if **rpow_2** is specified.


Axiparabola--echelon flying-focus pulse
***************************************

The axiparabola--echelon flying-focus pulse is selected by setting **type** to "axi-echelon flying focus".  This pulse creates a flying focus (with focal velocity in the neighborhood of the speed of light) using a combination of an axiparabola and an echelon.\ [3]_   In addition to all the parameters available for both a `standard pulse`_ and an `ideal flying focus pulse`_\ , the parameters below are also available.

Available parameters: **echelon**\ , **Rap** \, **Lap**\ , **lambdaD**\ , **nlambfact**\ , **nr_sag**, **Rmin**\ =\ *None*, **Rmin_pow**\ =\ *rpow*

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

   **Rmin_pow** : float, optional
      The power for the radial profile of the inner cutoff.  Defaults to **rpow**, but having this as a separate parameter allows for a sharp cutoff in the center and a more gradual one (e.g., Gaussian) on the outer edge.

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

Available parameters: **f0**\ , **nr_lens**\ , **rmaxf_lens**, **rpow**\ =\ *2*, **rpow_2**\ =\ *None*, **w0_2**\ =\ *None*

   **f0** : float
      Nominal focal length (m) of the focusing optic.

   **nr_lens** : int
      Number of grid points in the lens plane.

   **rmaxf_lens** : float
      Factor (that multiplies ``w0`` of the pulse at the lens plane) to determine ``rmax`` of the lens, i.e., ``rmax_lens = rmaxf_lens * w0``.

   **rpow** : float, optional
      Power for the radial profile of the field at the lens, which is proportional to :math:`\exp[-(r_\mathrm{lens}/w_0)^{r_\mathrm{pow}}]`\ .  The value of **rpow** defaults to 2.

   **rpow_2** : float, optional
      A second power to also multiply in to the field at the lens, similar to what is done for **rpow**\ .  The value of **rpow_2** defaults to *None* and has no effect.  This parameter could be useful, for example, to simulate a Gaussian pulse profile incident on a lens with a hard radial cutoff.  In that case, the parameters **rpow** = 2 and **rpow_2** = 40 could be used.

   **w0_2** : float, optional
      The spot size in the exponential applied with the **rpow_2** parameter.  Must be specified if **rpow_2** is specified.


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

The custom pulse is for use only when doing machine-learned optimization problems.  It allows for the user to completely specify the function (often related to one of the other pulse initialization methods) to use for initializing the laser pulse.  Although this offers the user complete freedom, the function ``init_E_with_coefs`` is often used, and is defined in ``helpers.py``.  This function utilizes the below pulse parameters.

Standard available parameters: **lambda0**\ , **tcent**\ , **tpulse**\ , **tpow**\ , **wf**\ , **zf**\ , **phase**\ , **f0**\ , **nr_lens**\ , **rmaxf_lens**, **rpow**\ =\ *2*, **rpow_2**\ =\ *None*, **w0_2**\ =\ *None*

   **lambda0** : float
      The wavelength (m) of the pulse.

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

   **f0** : float
      Nominal focal length (m) of the focusing optic.

   **nr_lens** : int
      Number of grid points in the lens plane.

   **rmaxf_lens** : float
      Factor (that multiplies ``w0`` of the pulse at the lens plane) to determine ``rmax`` of the lens, i.e., ``rmax_lens = rmaxf_lens * w0``.

   **rpow** : float, optional
      Power for the radial profile of the field at the lens, which is proportional to :math:`\exp[-(r_\mathrm{lens}/w_0)^{r_\mathrm{pow}}]`\ .  The value of **rpow** defaults to 2.

   **rpow_2** : float, optional
      A second power to also multiply in to the field at the lens, similar to what is done for **rpow**\ .  The value of **rpow_2** defaults to *None* and has no effect.  This parameter could be useful, for example, to simulate a Gaussian pulse profile incident on a lens with a hard radial cutoff.  In that case, the parameters **rpow** = 2 and **rpow_2** = 40 could be used.

   **w0_2** : float, optional
      The spot size in the exponential applied with the **rpow_2** parameter.  Must be specified if **rpow_2** is specified.



.. rubric:: References

.. [1] A\. Couairon, *et al*\ ., `"Practitioner’s guide to laser pulse propagation models and simulation," <https://doi.org/10.1140/epjst/e2011-01503-3>`_ *Eur. Phys. J.: Spec. Top.* **199**\ (1), 5-76 (2011).

.. [2] D\. Ramsey, *et al*\ ., `"Exact solutions for the electromagnetic fields of a flying focus," <https://doi.org/10.1103/PhysRevA.107.013513>`_ *Phys. Rev. A* **107**\ (1), 013513 (2023).

.. [3] M\. V. Ambat, *et al*\ ., `"Programmable-trajectory ultrafast flying focus pulses," <https://doi.org/10.1364/OE.499839>`_ *Opt. Express* **31**\ (19), 31354 (2023).

.. [4] A\. T. Friberg, `"Stationary-phase analysis of generalized axicons," <https://doi.org/10.1364/JOSAA.13.000743>`_ *J. Opt. Soc. Am. A* **13**\ (4), 743 (1996).
