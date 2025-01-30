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
  x_limit: [-0.03, 0.04]
```
The above defines an aperture with the name **ap1**. 
```{code} yaml
Aperture:
  name: ap2
  inherit: ap1
  y_limit: [-0.02, 0.05]
```
And the above defines a new aperture group which inherits from **ap1**.

Naming a parameter group is only needed if the parameter group is defined outside of an element.
```{code} yaml
Element:
  name: q1
  Aperture: 
    x_limit: [-0.03, 0.04]
    y_limit: [-0.02, 0.03]
```
And an element can inherit a parameter group from another element:
```{code} yaml
Element:
  name: q2
  Aperture:
    inherit: q1.Aperture
```

For an element to inherit all parameter groups from another element, just inherit the element itself:
```{code} yaml
Element:
  name: q3
  inherit: q2
```

## Aperture Parameter Group

Components:
```{code} yaml
x_limit                      # [m] Vector of two real numbers
y_limit                      # [m] Vector of two real numbers
shape                        # Switch
location                     # Switch
aperture_shifts_with_body    # Boolian
vertices                     # Structure
material                     # String
thickness                    # [m] Real number
```

### location component

The aperture location is set by the `location` parameter. Possible values are
```{code} yaml
ENTRANCE_END   # Body entrance end (default)
CENTER         # Element center
EXIT_END       # Body exit end
BOTH_ENDS      # Both ends
NOWHERE        # No location
EVERYWHERE     # Everywhere
```
The default is `ENTRANCE_END`.

### shape component

```{figure} figures/apertures.svg
:width: 90%
:name: f:aperture

A) RECTANGULAR and ELLIPTICAL apertures. As drawn, `x_limit[1]` and `y_limit[1]` are 
negative and `x_limit[2]` and `y_limit[2]` are positive. B) The `vertices` aperture is defined
by a set of vertices.
```

The `shape` parameter selects the shape of the aperture. Possible values are:
```{code} yaml
RECTANGULAR   # Rectangular shape.
ELLIPTICAL    # Elliptical shape.
VERTICES      # Shape defined by set of vertices.
CUSTOM_SHAPE  # Shape defined outside of the lattice standard.
```

### x_limit and y_limit components

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

### vertices component

The `VERTICES` setting for `shape` is for defining an aperture using a 
set of vertex points as illustrated in {numref}`f:aperture`B. 
Between vertex points, the aperture can can follow a straight line or the arc of an ellipse. 
The vertex points are specified by setting the `vertices` parameter. This parameter has three
subcomponents
```{code} yaml
center              # [m] Vector of [x, y] center point.
absolute_vertices   # Boolian. Default is False.
list                # Struct. Ordered list of vertex points.
```
The `list` vector can have the components:
```{code} yaml
point       # [m] Vector of two real numbers.
radius      # [m] Vector of single or two real numbers.
tilt        # [rad/2pi] Ellipse tilt angle.
```
Example:
```{code} yaml
Aperture:
  vertices:
    center: [-0.045, 0.011]
    absolute_vertices: True
    list:
      - point: [0.023, 0.069]      # V1
      - point: [-0.025, 0.067]     # V2
      - radius: [0.08, 0.04]
      - tilt: 0.12
      - point: [-0.088, 0.036]     # V3
      - point: [-0.088, -0.021]    # V4
      - point: [0.023, -0.033]     # V5
```
This corresponds roughly to what is shown in {numref}`f:aperture`.

If the boolean `absolute_vertices` is set `False`, which is the default,
the vertex point positions are with respect to the `center` point. 
That is, the vertex point positions in absolute terms are the positions given with each vertex plus
the position of the `center`. If `absolute_vertices` is `True`, the vertex point positions 
are independent of the `center` point. The default position of the `center` is the origin.

The `list` component of `vertices` contains an ordered  list of vertex 
`point`s with each one specifying an {math}`(x, y)` position. 
In between any two `point`s, there can optionally be a single `radius` element.
If there is a `radius` element, there can also be a  single `tilt` element between
the `point`s but `tilt` can only be present if there is a `radius` present.
There can also be a `radius` element and `tilt` element after the last `point` in the `list`. 
If these are present, they are considered to be between the last and fist `point`s in the list.

The aperture is constructed by connecting consecutive `point`s in the `list` along with
connecting the last `point` to the first. If there is no `radius` between consecutive `point`s,
the aperture follows a straight line between the points. If there is a `radius` element between
two points, the aperture is either a circular arc or a section of an ellipse. 
A `radius` can have either a single value or a list of two values. If a `radius` has a single
value, this is the radius of the circular arc connecting the vertices. In this case, the `tilt`
element may not be present. If the `radius` has two values, these are the half lengths of the 
principal diameters. If the `tilt` is zero (the default), the ellipse is upright with the first 
`radius` value being the {math}`x`-axis half width and the second value being the {math}`y`-axis
half width. A non-zero `tilt` value rotates the ellipse by that amount. 

In order to be able to quickly calculate whether a particle is inside or outside the
aperture, the aperture shape has some restrictions. For one, 
the vertex points must be arranged so that the polar coordinate angle of the vertex points
with respect to the `center` point is increasing. That is, for vertices {math}`v_i` and
{math}`v_{i+1}` that are at angles {math}`\theta_i` and {math}`\theta_{i+1}` with respect
to the `center` point:
```{math}
  0 < \theta_{i+1} - \theta_{i} \pmod{2\pi} < \pi
```
Another restriction is that any half-line drawn from the `center` point out to infinity intersects
the aperture at exactly one point. Finally, for a circular or elliptical arc, of
the four possible solutions that connect the two vertex points at the ends of the arc, 
the arc used is the arc of minimal length such that the center of the ellipse is on the same side
as the `center` point with respect to a line drawn through the two points.

When using vertices, an efficient way to determine if a point {math}`(x, y)` is within the aperture
is the following. Note: The computation is with respect to the `center` point.
```{code} yaml
  0) Determine the polar coordinate angles {math}`\theta_i` of the vertices. These angles can be 
stored so this computation only has to be done once.
  1) Determine the angle of the particle.
  2) A simple binary search will give the neighboring pair of vertices the particle's angle falls between.
  3) A simple geometric calculation now determines where the half line drawn from the `center`
and through the particle point intersects the aperture.
  4) The particle is outside the aperture if and only if the intersection point is between the 
particle point and the `center` point.
```

