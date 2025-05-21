``medium``
==========

The medium section specifies the gas that any radiation will propagate through.

Available parameters: **atomdensity**\ =\ *None*\ , **atom**\ , **nu_ei**\ =\ *1.0e13*

   **atomdensity** : float, optional
      The density of atoms specified by the **atom** parameter.  If **atomdensity** is left unspecified, the density will be set to standard temperature and pressure for the given **atom**\ .

   **atom** : str
      The atom to use for the medium.  Currently accepted values are *"hydrogen"*\ , *"helium"*\ , and *"argon"*\ .

   **nu_ei** : float, optional
      The electron--ion collision frequency (s\ :math:`^{-1}`\ ).  The value of **nu_ei** defaults to 1.0e13.

.. note::

   The ``vg0`` parameter that is stored into the ``medium`` section of the dictionary in post-processing is the group velocity of the frequency specified by ``grid.lambda0``, which will be the frame velocity if ``grid.vf`` is left unspecified.
