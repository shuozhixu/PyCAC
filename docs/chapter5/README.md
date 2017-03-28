# Command

This chapter describes how a PyCAC input script `cac.in` is formatted and the input script commands used to define a CAC simulation.

CACS reads the entire `cac.in` and then performs a simulation with all the settings. Thus, the sequence of commands does not matter.

A line with the "\#" character in the beginning is treated as a comment and discarded.

Many input script errors are detected and an Error or Warning message is printed.

Below is a list of all CACS commands, grouped by category.

Simulation box:

[boundary](boundary.md), [box](box.md), [ele\_size](ele_size.md), [grain\_dir](grain_dir.md), [grain\_mat](grain_mat.md), [grain\_move](grain_move.md), [grain\_num](grain_num.md), [grain\_uc](grain_uc.md), [uc\_num](uc_num.md), [modify\_num](modify_num.md), [modify\_\#](modify_#.md), [zigzag](zigzag.md)

Materials:

[bd\_group](bd_group.md), [lattice](lattice.md), [mass](mass.md), [potential](potential.md)

Settings:

[cal\_num](cal_num.md), [cal\_\#](cal_#.md), [constrain](constrain.md), [simulator](simulator.md), [dump\_num](dump_num.md), [ensemble](ensemble.md), [force\_dir](force_dir.md), [group](group.md), [group\_\#](group_#.md), [limit](limit.md), [mass\_mat](mass_mat.md), [neighbor](neighbor.md), [temperature](temperature.md)

Actions:

[deform](deform.md), [dynamics](dynamics.md), [minimize](minimize.md), [refine](refine.md), [restart](restart.md), [run](run.md)

Miscellanies:

[convert](convert.md), [debug](debug.md)
