# Element Parameters

Lattice elements parameters are organized into **parameter groups**. 
All groups are organized as abstract syntax trees.
At the top level, there are the groups with names like `MagMultipole`, `ElecMultipole`, `RF`, `Alignment`, etc. 
By convention, group names use upper camel case and it is highly recommended that this convention
be followed but it is not mandatory.

There are two element parameters that are so common that they are not grouped. 
These element parameters are `L` (element length) and `name` (name of element).


## Naming and Inheriting Groups

Any group can be given a **name** and the values can be used in another group of the same type
using **import**.
For example:
```{code}
Aperture:
  name: ap1
  x_limit: [-0.03, 0.03]
```
The above defines an aperture with the name **ap1**. 
```{code}
Aperture:
  inherit: ap1
  y_limit: [-0.02, 0.02]
```
And the above defines a new aperture group which inherits from **ap1**.

## Aperture Parameter Group

Components:
```{code}
x_limit
y_limit
aperture_shape
aperture_at
aperture_shifts_with_body 
```