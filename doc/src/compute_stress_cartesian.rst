.. index:: compute stress/cartesian

compute stress/cartesian command
==================================

Syntax
""""""

.. code-block:: LAMMPS

   compute ID group-ID stress/cartesian args

* ID, group-ID are documented in :doc:`compute <compute>` command
* args = argument specific to the compute style

.. parsed-literal::

  *stress/cartesian* args = dim1 bin_width1 dim2 bin_width2 keyword
    dim1 = *x* or *y* or *z*
    bin_width1 = width of the bin
    dim2 = *x* or *y* or *z* or *NULL*
    bin_width2 = width of the bin
    keyword = *ke* or *pair* or *bond*

Examples
""""""""

.. code-block:: LAMMPS
   compute 1 all stress/cartesian x 0.1 NULL 0
   compute 1 all stress/cartesian y 0.1 z 0.1
   compute 1 all stress/cartesian x 0.1 NULL 0 ke pair

Description
"""""""""""

Compute *stress/cartesian* defines computations that calculate profiles of the
diagonal components of the local stress tensor over one or two Cartesian
dimensions, as described in :ref:`(Ikeshoji)<Ikeshoji2>`. The stress tensor is
split into a kinetic contribution :math:`P^k` and a virial contribution
:math:`P^v`. The sum gives the total stress tensor :math:`P = P^k+P^v`.
This compute obeys momentum balance through fluid interfaces. They use the
Irving--Kirkwood contour, which is the straight line between particle pairs.

.. versionadded:: TBD

   Added support for bond styles

This compute only supports pair and bond (no angle, dihedral, improper,
or kspace) forces. By default, if no extra keywords are specified, all
supported contributions to the stress are included (ke, pair, bond). If any
keywords are specified, then only those components are summed.

Output info
"""""""""""

The output columns for *stress/cartesian* are the position of the
center of the local volume in the first and second dimensions, number
density, :math:`P^k_{xx}`, :math:`P^k_{yy}`, :math:`P^k_{zz}`,
:math:`P^v_{xx}`, :math:`P^v_{yy}`, and :math:`P^v_{zz}`. There are 8
columns when one dimension is specified and 9 columns when two
dimensions are specified. The number of bins (rows) is
:math:`(L_1/b_1)(L_2/b_2)`, where :math:`L_1` and :math:`L_2` are the lengths
of the simulation box in the specified dimensions and :math:`b_1` and
:math:`b_2` are the specified bin widths. When only one dimension is
specified, the number of bins (rows) is :math:`L_1/b_1`.

This array can be output with :doc:`fix ave/time <fix_ave_time>`,

.. code-block:: LAMMPS

  compute p all stress/cartesian x 0.1
  fix 2 all ave/time 100 1 100 c_p[*] file dump_p.out mode vector

The values calculated by this compute are "intensive".  The stress
values will be in pressure :doc:`units <units>`. The number density
values are in inverse volume :doc:`units <units>`.

NOTE 1: The local stress does not include any Lennard-Jones tail
corrections to the stress added by the
:doc:`pair_modify tail yes <pair_modify>`
command, since those are contributions to the global system pressure.

NOTE 2: The local stress profiles generated by these computes are
similar to those obtained by the
:doc:`method-of-planes (MOP) <compute_stress_mop>`.
A key difference is that compute
:doc:`stress/mop/profile <compute_stress_mop>`
considers particles crossing a set of planes, while
*stress/cartesian* computes averages for a set of small volumes.
Moreover, this compute computes the diagonal components of the stress
tensor :math:`P_{xx}`, :math:`P_{yy}`, and :math:`P_{zz}`, while
*stress/mop/profile* computes the components
:math:`P_{ix}`, :math:`P_{iy}`, and :math:`P_{iz}`, where :math:`i` is the
direction normal to the plane.

More information on the similarities and differences can be found in
:ref:`(Ikeshoji)<Ikeshoji2>`.

Restrictions
""""""""""""

These computes calculate the stress tensor contributions for pair and bond
forces only (no angle, dihedral, improper, or kspace force).
It requires pairwise force calculations not available for most
many-body pair styles.

These computes are part of the EXTRA-COMPUTE package.  They are only
enabled if LAMMPS was built with that package.  See the :doc:`Build
package <Build_package>` doc page for more info.

Related commands
""""""""""""""""

:doc:`compute stress/atom <compute_stress_atom>`, :doc:`compute pressure <compute_pressure>`,
:doc:`compute stress/mop/profile <compute_stress_mop>`, :doc:`compute stress/spherical <compute_stress_curvilinear>`,
:doc:`compute stress/cylinder <compute_stress_curvilinear>`

----------

.. _Ikeshoji2:

**(Ikeshoji)** Ikeshoji, Hafskjold, Furuholt, Mol Sim, 29, 101-109, (2003).