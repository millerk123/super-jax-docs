grid
====

The ``grid`` section determines the speed of the moving frame, as well as the number of points in :math:`t`\ , :math:`z`\ , and :math:`r`\ .

Available parameters: **vf**\ =\ *None*\ , **lambda0**\ , **tmin**\ , **tmax**\ , **nt**\ , **rmax**\ , **nr**\ , **zmin**\ , **zmax**\ , **nz**\ , **ell**\ =\ *0*

   **vf** : float, optional
      The speed of the moving frame (in units of the speed of light).  If this parameter is left blank (recommended), the moving frame will be set to move at the group velocity of the wavelength specified by **lambda0** in the given medium.  This is ideal for stability, and the calculated value of **vf** can later be retrieved from the output data.

   **lambda0** : float
      The wavelength (m) that specifies the default frequency for calculation of the moving-frame velocity and for filtering.  This should ideally be set to the main frequency of the laser light.

   **tmin** : float
      The minimum time (s) used for the :math:`t` grid.

   **tmax** : float
      The maximum time (s) used for the :math:`t` grid.

   **nt** : int
      The number of points used for the time grid.  Since many FFTs will be taken in this dimension, the algorithm will be fastest if this is a power of 2.  Otherwise, a number of the form :math:`2^a 3^b 5^c 7^d 11^e 13^f`\ , where :math:`e+f` is either 0 or 1, will also be `reasonably efficient <https://www.fftw.org/fftw2_doc/fftw_3.html>`_\ .

   **rmax** : float
      The maximum radial coordinate (m) used for the :math:`r` grid.

   **nr** : int
      The number of points used for the radial grid.

   **zmin** : float
      The initial, minimum longitudinal coordinate (m) used for stepping in :math:`z`.

   **zmax** : float
      The final, maximum longitudinal coordinate (m) used for stepping in :math:`z`.

   **nz** : int
      The number of points used for stepping in :math:`z`.

   **ell** : int, default: 0
      The azimuthal index :math:`\ell` of the simulation, which can take on values of either 0 or 1.  If set to 0, the simulation corresponds to a cylindrically symmetric pulse.  If set to 1, a laser pulse initialized like a Laguerre--Gaussian mode with :math:`\ell = 1` will propagate properly.  Setting **ell** to 1, however, is an experimental mode, and most types of laser pulses will not work well.
