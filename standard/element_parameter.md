# Element Parameter Naming

Lattice elements parameters are organized into **parameter groups**. All groups are organized as abstract syntax trees.
At the top level, there are the groups with names like `MagMultipoleGroup`, `ElecMultipoleGroup`, `RFGroup, `BendGroup`, `AlignmentGroup`, etc. 
Group names use Upper camel case and have `Group` at the end of the name.

A dot `.` is used as a separator between levels in a group. 
For example, `BendGroup.angle`, `BendGroup.e1`, `RFGroup.frequency`, etc. Using lower case for second and lower level names is encouraged but not mandated.

There are two element parameters that are so common that they are not grouped. These element parameters are `L` (element length) and `Name` (name of element).
