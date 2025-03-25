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

**Available parameters:** **experiment**\ , **run**

   **experiment : str**
      Name of the experiment for this simulation.

   **run : str**
      Name of the run for this simulation.

``grid``
-----

The grid section determines the speed of the moving frame, as well as the number of points in :math:`t`\ , :math:`z`\ , and :math:`r`\ .

**Available parameters:** **vf**\ *=None*\ , **lambda0**\ , **tmin**\ , **tmax**\ , **nt**\ , **xmin**\ , **xmax**\ , **nx**\ , **max_steps**\ *=8192*\ , **ell**\ *=0*

   **vf : float, optional**
      The speed of the moving frame (in units of the speed of light).  If this parameter is left blank (recommended), the moving frame will be set to move at the group velocity of the wavelength specified by **lambda0** in the given medium.  This is ideal for stability, and the calculated value of **vf** can later be retrieved from the output data.

   **lambda0 : float**
      The wavelength (m) that specifies the default frequency for calculation of the moving-frame velocity and for filtering.  This should ideally be set to the main frequency of the laser light.

   **tmin : float**
      The minimum time (s) used for the time grid.

   **tmax : float**
      The maximum time (s) used for the time grid.

   **nt : int**
      The number of points used for the time grid.  Since many FFTs will be taken in this dimension, the algorithm will be fastest if this is a power of 2.  Otherwise, a number of the form :math:`2^a 3^b 5^c 7^d 11^e 13^f`\ , where :math:`e+f` is either 0 or 1, will also be `reasonably efficient <https://www.fftw.org/fftw2_doc/fftw_3.html>`_\ .

   **xmin : floats [zmin, rmin]**
      The minimum spatial coordinates (m) in :math:`z` and :math:`r`\ , respectively.  The value of **rmin** should be set to 0.

   **xmax : floats [zmax, rmax]**
      The maximum spatial coordinates (m) in :math:`z` and :math:`r`\ , respectively.

   **nx : ints [nz, nr]**
      The number of points used in :math:`z` and :math:`r`\ , respectively.

   **max_steps : int, default: 8192**
      The Diffrax ``diffeqsolve`` function requires the **max_steps** parameter, which is the maximum number of steps to take before quitting the computation unconditionally.  If **nx[0]**, i.e., the **nz** parameter, is set to be larger than 8192, then **max_steps** should also be increased accordingly.

   **ell : int, default: 0**
      The azimuthal index :math:`\ell` of the simulation, which can take on values of either 0 or 1.  If set to 0, the simulation corresponds to a cylindrically symmetric pulse.  If set to 1, a laser pulse initialized like a Laguerre--Gaussian mode with :math:`\ell = 1` will propagate properly.  Setting **ell** to 1, however, is an experimental mode, and most types of laser pulses will not work well.
