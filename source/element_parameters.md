# Element Parameters

Lattice elements parameters are organized into **parameter groups**. 
All groups are organized as abstract syntax trees.
At the top level, there are the groups with names like `MagMultipole`, `ElecMultipole`, `RF`, `Alignment`, etc. 
By convention, group names use upper camel case and it is highly recommended that this convention
be followed but it is not mandatory.

There are two element parameters that are so common that they are not grouped. 
These element parameters are `L` (element length) and `name` (name of element).


## Naming and Inheriting Parameter Groups

Any group can be given a **name** and the values can be used in another group of the same type
using **import**.
For example:
```{code} yaml
Aperture:
  name: ap1
  x_limit: [-0.03, 0.03]
```
The above defines an aperture with the name **ap1**. 
```{code} yaml
Aperture:
  name: ap2
  inherit: ap1
  y_limit: [-0.02, 0.02]
```
And the above defines a new aperture group which inherits from **ap1**.

Naming a parameter group is only needed if the parameter group is defined outside of an element.
```{code} yaml
element:
  name: q1
  Aperture: 
    x_limit: [-0.03, 0.03]
    y_limit: [-0.02, 0.02]
```
And an element can inherit a parameter group from another element:
```{code} yaml
element:
  name: q2
  Aperture:
    inherit: q1.Aperture
```

For an element to inherit all parameter groups from another element, just inherit the element itself:
```{code} yaml
element:
  name: q3
  inherit: q2
```

## Aperture Parameter Group

Components:
```{code} yaml
x_limit
y_limit
aperture_shape
aperture_at
aperture_shifts_with_body
wall2d
material
thickness
```

### aperture_at

The aperture location is set by the `aperture_at` parameter. Possible values are
```{code} yaml
ENTRANCE_END   # Body entrance end (default)
CENTER         # Element center
EXIT_END       # Body exit end
BOTH_ENDS      # Both ends
NOWHERE        # No location
EVERYWHERE     # Everywhere
```
The default is `ENTRANCE_END`.

### aperture_shape

```{figure} figures/apertures.svg
:width: 90%
:name: f:aperture

A) RECTANGULAR and ELLIPTICAL apertures. As drawn, `x_limit[1]` and `y_limit[1]` are 
negative and `x_limit[2]` and `y_limit[2]` are positive. B) The `wall2d` aperture is defined
by a set of vertices.
```

The `aperture_shape` parameter selects the shape of the aperture. Possible values are:
```{code} yaml
RECTANGULAR   # Rectangular shape.
ELLIPTICAL    # Elliptical shape.
WALL2D        # Shape defined by set of vertices.
CUSTOM_SHAPE  # Shape defined outside of the lattice standard.
```

### x_limit and y_limit

For `RECTANGULAR` and `ELLIPTICAL` shapes the `x_limit` and `y_limit` parameters are
used to calculate the aperture as shown in {numref}`f:aperture`A. 
For an `ELLIPTICAL` aperture, a particle with position {math}`(x, y)` is outside of the aperture if any 
one of the following four conditions is true:
```{code}
  1) x < 0 and y < 0 and (x/x_limit[1])^2 + (y/y_limit[1])^2 > 1 
  2) x < 0 and y > 0 and (x/x_limit[1])^2 + (y/y_limit[2])^2 > 1
  3) x > 0 and y < 0 and (x/x_limit[2])^2 + (y/y_limit[1])^2 > 1
  4) x > 0 and y > 0 and (x/x_limit[2])^2 + (y/y_limit[2])^2 > 1
```
For a `RECTANGULAR` aperture the corresponding four conditions are:
```{code}
  1) x < x_limit[1]
  2) x > x_limit[2]
  3) y < y_limit[1]
  4) y > y_limit[2]
```

Default values for the limits are `[-Inf, Inf]` for both `x_limit` and `y_limit`.

### misalignment_moves_aperture

The `misalignment_moves_aperture` parameter determines whether misaligning an element 
affects the placement of the aperture. The default is `False`. 
A common case where `misalignment_moves_aperture` would be `False` is when a beam pipe,
which incorporates the aperture, is not physically touching the surrounding magnet element. 
When tracking a particle, assuming that there are only apertures at the element ends, 
the order of computation with `misalignment_moves_aperture` set to `False` could be
```{code} yaml
  1) Start at upstream end of element
  2) Check upstream aperture if there is one.
  3) Convert from branch coordinates to body coordinates.
  4) Track through the element body.
  5) Convert from body coordinates to branch coordinates.
  6) Check downstream aperture if there is one.
  7) End at downstream end of element.
```
With `misalignment_moves_aperture` set to `True`, the computation order could be
```{code} YAML
  1) Start at upstream end of element
  2) Convert from branch coordinates to body coordinates.
  3) Check upstream aperture if there is one.
  4) Track through the element body.
  5) Check downstream aperture if there is one.
  6) Convert from body coordinates to branch coordinates.
  7) End at downstream end of element.
```

### material

The `material` parameter sets the material of the aperture. 
Using chemical formulas like `Cu` and `Fe` are the most portable.

### wall2d

The `WALL2D` setting for `aperture_shape` is for defining an aperture using a 
set of vertex points as illustrated in {fignum}`f:aperture`B. 
Between vertex points, the aperture can can follow a straight line or the arc of an ellipse. 
The vertex points are specified by setting the `wall2d` parameter. This parameter has three
subcomponents
```{code} yaml
center              # (x, y) center pointer.
absolute_vertices   # Boolian. Default is False.
vertices            # Ordered list of vertex points.
```
Example:
```{code} yaml
Aperture:
  wall2d:
    center: [-0.045, 0.011]
    absolute_vertices: True
    vertices:
      - point: [0.023, 0.069]      # V1
      - point: [-0.025, 0.067]     # V2
      - radius: [0.08, 0.04]
      - tilt: 0.12
      - point: [-0.088, 0.036]     # V3
      - point: [-0.088, -0.021]    # V4
      - point: [0.023, -0.033]     # V5
```
This corresponds roughly to what is shown in {figref}`f:aperture`.

If the boolean `absolute_vertices` is set `False`, which is the default,
the vertex point positions are with respect to the `center` point. 
That is, the vertex point positions in absolute terms are the positions given with each vertex plus
the position of the `center`. If `absolute_vertices` is `True`, the positions ofby the
vertex points are independent of the `center`.





