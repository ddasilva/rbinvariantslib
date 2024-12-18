Radiation Belt Invariants Library
==================================

.. image:: https://github.com/ddasilva/rbinvariantslib/actions/workflows/run_test.yml/badge.svg
    :target: https://github.com/ddasilva/rbinvariantslib/actions
    :alt: CI Status


.. image:: https://img.shields.io/badge/DOI-10.1029/2023JA032397-blue
    :target: https://doi.org/10.1029/2023JA032397
    :alt: Paper DOI

.. image:: https://img.shields.io/badge/License-BSD%203-green
   :alt: BSD-3	   
	  
.. note:: 
    
    This package provides tools for radiation belt physicists to calculate the adiabiatic invariants K and L* from gridded models of Earth's magnetic field. This package supports the `T96 <https://geo.phys.spbu.ru/~tsyganenko/empirical-models/magnetic_field/t96/>`_ and `TS05 <https://geo.phys.spbu.ru/~tsyganenko/empirical-models/magnetic_field/ts05/>`_ empirical Tsyganenko magnetic field models, `SWMF <https://clasp.engin.umich.edu/research/theory-computational-methods/space-weather-modeling-framework/>`_ and `LFM <https://doi.org/10.1016/j.jastp.2004.03.020>`_ MHD simulation output, and data on an arbitrary structured grid. 

    `[Paper] <https://doi.org/10.1029/2023ja032397>`_ `[Poster] <_static/poster.pdf>`_

**Table of Contents**

.. toctree::
  :maxdepth: 2

  whatsnew/index
  methodology.rst   
  citing.rst          
  rbinvariantslib.rst

Installing
-------------
This module can be installed using pip and PyPI.

.. code::

   $ pip install rbinvariantslib

Brief Tour
-------------

Calculating L* from TS05
+++++++++++++++++++++++++
Below is code which calculates L* using the magnetic fields obtain from TS05 and placed on a regular grid, for a particle observated with a pitch angle of 60° observed at (-6.6, 0, 0) R :sub:`E` (SM coordinate system).

.. code-block:: python

    from rbinvariantslib import models, invariants
    from datetime import datetime
    import numpy as np

    # Get TS05 model input parameters from CDAWeb API
    time = datetime(2015, 10, 2)
    params = models.get_tsyganenko_params(time)
    
    # Evaluate TS05 model on regular grid 
    axis = np.arange(-10, 10, 0.50)
    x, y, z = np.meshgrid(axis, axis, axis)
    model = models.get_tsyganenko(
        "TS05", params, time,
        x_re_sm_grid=x,
        y_re_sm_grid=y,
        z_re_sm_grid=z,
        inner_boundary=1
    )

    # Calculate L* 
    result = invariants.calculate_LStar(
        model,
        starting_point=(-6.6, 0, 0),
        starting_pitch_angle=60
    )

    print(f"L* = {result.LStar}")



Calculating K from SWMF 
+++++++++++++++++++++++++
This code calculates the second adiabatic invariant K for a particle bouncing through (-6.6, 0, 0) R :sub:`E` (SM coordinate system) and mirroring at at 50° magnetic latitude, using magnetic fields from SWMF simulation output in CDF format (as obtained from the CCMC).

.. code-block:: python

    from rbinvariantslib import models, invariants

    model = models.get_model(
        "SWMF_CDF",
        "3d__var_1_e20151221-001700-014.out.cdf"
    )

    result = invariants.calculate_K(
        model,
        starting_point=(-6.6, 0, 0),
        mirror_latitude=50
    )

    print(f"K = {result.K}")


