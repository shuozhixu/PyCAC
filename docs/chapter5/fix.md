## fix

### Syntax

	fix group_name boolean_release boolean_def
	    assign_style assign_x assign_y assign_z disp_lim
	    time time_start time_end
	    boolean_grad
	    grad_ref_axis grad_assign_axis
	    grad_ref_l grad_ref_u

* `group_name` = a string (length <= 30)

* `boolean_release`, `boolean_def`, `boolean_grad` = _t_ or _f_

		t is true
		f is false

* `assign_style` = _disp_ or _force_

* `assign_x`, `assign_y`, `assign_z` = real number or _null_

* `disp_lim` = non-negative real number

* `time_start`, `time_end` = non-negative integer

* `grad_ref_axis`, `grad_assign_axis` = _1_ or _2_ or _3_

* `grad_ref_l`, `grad_ref_u` = real number or _inf_

### Examples

	fix big_sphere t t disp 0. null 0. 5. time 0 2500 f
	fix blue_cone t t disp 5. 0. 0. 10. time 0 2500 t 2 1 50. 60.
	fix top_box t t force 0. 1. 0. 3. time 0 2500 t 3 2 100. 10.

### Description

This command applies displacements and/or forces to new groups and restart groups, the numbers of which are provided in the [group_num](group_num.md) command. The number of `fix` commands is [`fix_number`](group_num.md). The new groups are created by first providing the elements/nodes/atoms information in the [group](group.md) command, while the same information for the restart groups, which are introduced when [`restart_group_number`](group_num.md) > 0, is read from `group_in_*.id`, where `*` is a positive integer starting from [`new_group_number`](group_num.md) + 1.

When the groups are at the simulation cell boundaries, this command is useful in applying displacement, traction, or mixed boundary conditions.

`group_name` must match one of the [new groups](group.md) or [restart groups](group_num.md). All in this command take effect only when `time_start` < [simulation step](run.md) < `time_end`, unless stated otherwise.

when [simulation step](run.md) > `time_end`, the group is no longer assigned a displacement/force if `boolean_release` = _t_; the group is assigned a displacement/force vector [`assign_x`, `assign_y`, `assign_z`] whose non-_null_ components are zeroed.

When `boolean_def` = _t_, the group is deformed [along with the simulation box](deform.md). The deformation-induced displacement is added on top of the assigned displacement/force.

`assign_style` = _disp_ or _force_, meaning that a displacement or force vector [`assign_x`, `assign_y`, `assign_z`], in Angstrom/[`time_step`](run.md) or eV/Angstrom, is applied to all nodes/atoms in the group at each [simulation step](run.md), after their [interatomic potential](potential.md)-based displacement/force is discarded. If any component of the displacement/force vector is _null_, no displacement/force is assigned to this component. In the first example, `big_sphere` is fixed along the $$x$$ and $$z$$ directions but not along the $$y$$ direction.

`disp_lim` is the upper bound of the magnitude of the total group displacement, in units of [`lattice_constant`](lattice.md). If the total displacement magnitude (in Angstrom instead of in Angstrom/[`time_step`](run.md)) is larger than `disp_lim`, the displacement vector is zeroed, regardless of whether `time_end` is reached or what value `boolean_release` is. `disp_lim` is irrelevant when `assign_style` = _force_. However, it needs to be provided regardless.

When `boolean_grad` = _f_, the same displacement/force vector [`assign_x`, `assign_y`, `assign_z`] is assigned to all nodes/atoms of the group; the following options, including `grad_ref_axis`, `grad_assign_axis`, `grad_ref_l`, and `grad_ref_u`, become irrelevant and do not need to provided.

When `boolean_grad` = _t_, the displacement/force is assigned to the group gradiently, i.e., different elements/nodes/atoms in the group may have a different [`assign_x`, `assign_y`, `assign_z`] vector. The `grad_assign_axis` component of the displacement/force vector is linearly applied to the group based on the positions of elements/nodes/atoms along the `grad_ref_axis` direction. `grad_ref_l` and `grad_ref_u` are the bounds of the graded displacement/force, in units of the component of the [lattice periodicity length vector $$\vec{l'}_0$$](../chapter8/lattice-space.md) along the `grad_ref_axis` direction, with _inf_ referring to the lower (`grad_ref_l`) and upper (`grad_ref_u`) simulation cell boundaries.

If `grad_ref_l` < `grad_ref_u`, the elements/nodes/atoms located at or below `grad_ref_l` are assigned a zero displacement/force vector, i.e., fixed; those located at or above `grad_ref_u` are assigned [`assign_x`, `assign_y`, `assign_z`]. If `grad_ref_l` > `grad_ref_u`, the elements/nodes/atoms located at or above `grad_ref_l` are assigned a zero displacement/force vector, i.e., fixed; those located at or below `grad_ref_u` are assigned [`assign_x`, `assign_y`, `assign_z`]. In any case, the elements/nodes/atoms located between `grad_ref_l` and `grad_ref_u` are assigned a vector whose `grad_assign_axis` component is linearly graded while the other two components remain the same with respect to [`assign_x`, `assign_y`, `assign_z`].

In the second example, the elements/nodes/atoms which are located below $$50.0\cdot\vec{l'}_0[2]$$ along the _y_ axis (because `grad_ref_axis` = _2_) are assigned a zero displacement vector; those located above $$60.0\cdot\vec{l'}_0[2]$$ along the _y_ axis are assigned [`assign_x`, `assign_y`, `assign_z`]; those in between are assigned a linearly graded displacement vector whose _x_ component (because `grad_assign_axis` = _1_) is varied between zero and `assign_x` while its _y_ and _z_ components are `assign_y` and `assign_z`, respectively.

In the third example, the elements/nodes/atoms which are located below $$10.0\cdot\vec{l'}_0[3]$$ along the _z_ axis (because `grad_ref_axis` = _3_) are assigned [`assign_x`, `assign_y`, `assign_z`]; those located above $$100.0\cdot\vec{l'}_0[3]$$ along the _z_ axis are assigned a zero force vector; those in between are assigned a linearly graded force vector whose _y_ component (because `grad_assign_axis` = _2_) is varied between zero and `assign_y` while its _x_ and _z_ components are `assign_x` and `assign_z`, respectively.

Note that for each group concerned in this command, the displacement and force vectors are added to relevant nodes/atoms after their interatomic potential-based displacement/force vectored are zeroed. In particular, the force, stress, and energy are zeroed if `assign_style` = _disp_; the force, stress, and energy are replaced with those induced by this command if `assign_style` = _force_. In both cases, the velocity vectors are also zeroed in [dynamic and hybrid CAC](simulator.md).

In this sense, if the same atoms/nodes are included in multiple [groups](group.md) that are also concerned in this command, those appearing in the later fix commands will provail. For example, if a node is assigned a displacement vector in the first fix command, a force vector in the second fix command, and another force vector in the third fix command, the force vector in the last fix command will be imposed. As another example, if an atom is assigned a force vector in the fourth fix command, and a displacement vector in the fifth fix command, the force/stress/energy vector of that atom will be zeroed. To avoid unintended effects, users are advised to carefully check if the same nodes/atoms are involved in different fix commands.

### Related commands

There cannot be fewer `fix` commands than [`fix_number`](group_num.md), which should not be larger than [`new_group_number` + `restart_group_number`](group_num.md). When there are more `fix` commands in `cac.in` than [`fix_number`](group_num.md), those appearing later will be ignored.

Note that all groups do not necessarily have corresponding `fix` command. The purpose of having a group that does not have a correpsonding `fix` command is to [calculate](cal.md) certain mechanical properties, e.g., energy, force, and stress, of the nodes/atoms it contains.

### Related files

`fix.f90`, `fix_displacement.f90`, and `fix_force.f90`

### Default

None.