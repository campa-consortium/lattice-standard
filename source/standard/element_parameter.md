# Element Parameter Naming

Lattice elements parameters are organized into **parameter groups**. All groups are organized as abstract syntax trees.
At the top level, there are the groups with names like `MagMultipole`, `ElecMultipole`, `RF, `Bend`, `Alignment`, etc. 
Group names use Upper camel case.

A dot `.` is used as a separator between levels in a group. 
For example, `Bend.angle`, `Bend.e1`, `RF.frequency`, etc. Using lower case for second and lower level names is encouraged but not mandated.

There are two element parameters that are so common that they are not grouped. These element parameters are `L` (element length) and `Name` (name of element).
