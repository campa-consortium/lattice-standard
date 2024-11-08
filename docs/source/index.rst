.. Lattice Standard documentation master file, created by
   sphinx-quickstart on Wed Nov  6 15:13:18 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Lattice Standard's documentation!
============================================

Example of math rendering:

.. math::
   KL\left(\hat{y} || y\right) = \sum_{c=1}^{M}\hat{y}_c \log{\frac{\hat{y}_c}{y_c}}
   :label: kl

.. math::
   JS\left(\hat{y} || y\right) = \frac{1}{2}\left(KL\left(y||\frac{y+\hat{y}}{2}\right) + KL\left(\hat{y}||\frac{y+\hat{y}}{2}\right)\right)
   :label: js

Equations such as :eq:`kl` and :eq:`js` can be referenced with labels.

However, labeling does not seem to work properly with all math environments (e.g., ``split``, ``align``, etc.):

.. math::
   :label: kljs

   KL\left(\hat{y} || y\right) & = \sum_{c=1}^{M}\hat{y}_c \log{\frac{\hat{y}_c}{y_c}} \\
   JS\left(\hat{y} || y\right) & = \frac{1}{2}\left(KL\left(y||\frac{y+\hat{y}}{2}\right) + KL\left(\hat{y}||\frac{y+\hat{y}}{2}\right)\right)

Other features of the reStructuredText syntax include:

* **strong emphasis**;
* *emphasis*;
* inline ``code``;
* inline external `links <https://github.com/campa-consortium/lattice-standard>`_.

Literal code blocks with syntax highlighting are also available:

.. code-block:: python

   from mpi4py import MPI

   import amrex.space3d as amr

   # Initialize amrex::MPMD to establish communication across the two apps
   # However, leverage MPMD_Initialize_without_split
   # so that communication split can be performed using mpi4py.MPI
   amr.MPMD_Initialize_without_split([])
   # Leverage MPI from mpi4py to perform communication split
   app_comm_py = MPI.COMM_WORLD.Split(amr.MPMD_AppNum(), amr.MPMD_MyProc())
   # Initialize AMReX
   amr.initialize_when_MPMD([], app_comm_py)

   amr.Print(f"Hello world from pyAMReX version {amr.__version__}\n")
   # Create a MPMD Copier that gets the BoxArray information from the other (C++) app
   copr = amr.MPMD_Copier(True)
   # Number of data components at each grid point in the MultiFab
   ncomp = 2
   # Define a MultiFab using the created MPMD_Copier
   mf = amr.MultiFab(copr.box_array(), copr.distribution_map(), ncomp, 0)
   mf.set_val(0.0)

   # Receive ONLY the FIRST MultiFab component populated in the other (C++) app
   copr.recv(mf, 0, 1)

   # Fill the second MultiFab component based on the first component
   for mfi in mf:
       # Convert Array4 to numpy/cupy array
       mf_array = mf.array(mfi).to_xp(copy=False, order="F")
       mf_array[:, :, :, 1] = 10.0 * mf_array[:, :, :, 0]

   # Send ONLY the second MultiFab component to the other (C++) app
   copr.send(mf, 1, 1)

   # Finalize AMReX
   amr.finalize()
   # Finalize AMReX::MPMD
   amr.MPMD_Finalize()

.. toctree::
   :maxdepth: 2
   :caption: Contents:



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
