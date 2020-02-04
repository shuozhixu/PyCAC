
In a CAC simulation, a series of `cac_cg_#.vtk` and `cac_atom_#.vtk` files, containing the nodal/atomic position/energy/force/stress information, are [created on-the-fly](../chapter-3/output.md), with a frequency of [`output_freq`](../chapter-5/dump.md). A `model_cg.vtk` file, a `model_atom.vtk`, and possibly some `group_cg_#.vtk` and `group_atom_#.vtk` files (when the total number of [new group, restart group](../chapter-5/group_num.md) > 0) are also created in the beginning of the simulation. All these `*.vtk` files, with the [legacy formats](https://www.visitusers.org/index.php?title=ASCII_VTK_Files) as opposed to the [XML formats](http://www.vtk.org/Wiki/VTK_XML_Formats), can be read and analyzed by [ParaView](https://www.paraview.org), which provides a variety of analyses. In most cases, a CAC simulation cell contains both the atomistic and coarse-grained domain, and so a pair of `cac_cg_#.vtk` and `cac_atom_#.vtk` files (with the same integer `#`) should be loaded into ParaView at the same time.
