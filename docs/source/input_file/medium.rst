medium
======

The ``medium`` section specifies the gas that any radiation will propagate through.

Available parameters: **atomdensity**\ =\ *None*\ , **atom**\ , **nu_ei**\ =\ *1.0e13*\ , **density_func**\ =\ *1*\ , **density_upramp**\ =\ *None*\ , **density_downramp**\ =\ *None*

   **atomdensity** : float, optional
      The density of atoms specified by the **atom** parameter.  If **atomdensity** is left unspecified, the density will be set to standard temperature and pressure for the given **atom**\ .

   **atom** : str
      The atom to use for the medium.  Currently accepted values are *"hydrogen"*\ , *"helium"*\ , and *"argon"*\ .

   **nu_ei** : float, optional
      The electron--ion collision frequency (s\ :math:`^{-1}`\ ).  The value of **nu_ei** defaults to 1.0e13.

   **density_func** : str, optional
      A function of :math:`z` written in Python syntax for the density.  This should use ``jax.numpy`` functions invoked with ``jnp``\ .  For example, **density_func** could be set to *"jnp.power(jnp.sin(z), 2)"*\ .  Note, the **solver** must be set to *"direct_density"* in order for this parameter to be considered.

   **density_upramp** : float, [], optional
      An array of two floats, specifying the :math:`z_\mathrm{min}` and :math:`z_\mathrm{max}` values of a density upramp.  If **density_upramp** is set, then it overrides the **density_func** parameter.  This parameter can also be used in conjunction with **density_downramp** (the upramp must come before the downramp).  The idea is to easily implement a smoothly rising density profile for users.  The profile looks like :math:`10z^3-15z^4+6z^5`\ .  Note, the **solver** must be set to *"direct_density"* in order for this parameter to be considered.

   **density_downramp** : float, [], optional
      An array of two floats, specifying the :math:`z_\mathrm{min}` and :math:`z_\mathrm{max}` values of a density downramp.  If **density_downramp** is set, then it overrides the **density_func** parameter.  This parameter can also be used in conjunction with **density_upramp** (the downramp must come after the upramp).  The idea is to easily implement a smoothly falling density profile for users.  The profile looks like :math:`10z^3-15z^4+6z^5`\ .  Note, the **solver** must be set to *"direct_density"* in order for this parameter to be considered.

.. note::

   The ``vg0`` parameter that is stored into the ``medium`` section of the dictionary in post-processing is the group velocity of the frequency specified by ``grid.lambda0``, which will be the frame velocity if ``grid.vf`` is left unspecified.
