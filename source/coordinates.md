(c:coords)=
# Coordinates

%---------------------------------------------------------------------------------------------------
(s:coords)=
## Coordinate Systems

```{figure} figures/coordinates.svg
:width: 70%
:name: f:coords

The `floor` rectangular (Cartesian) coordinate system
is independent of the accelerator.  The `branch` curvilinear coordinate system follows the bends
of the accelerator. The `branch reference curve` is the {math}`x = y = 0` curve of the curvilinear coordinate
system. Each lattice element has `element body` coordinates which, if the element has no
alignment shifts (not "misaligned"), is the same as the `branch` coordinates.
```

%---------------------------------------------------------------------------------------------------

The lattice standard uses three coordinate systems as illustrated in the figure above. 
First, the `floor` coordinates are rectangular coordinates independent of the accelerator.
The position of the accelerator itself as well as external objects like the building the
accelerator is in  may be described using `floor` coordinates.

It is inconvenient to describe the position lattice elements and the position of a 
particle beam using the `floor` coordinate system so, for each branch,
a "[branch](#s:branch.coords)" coordinate system is used. This curvilinear coordinate
system defines the nominal position of the lattice elements. The relationship between the
`branch` and `floor` coordinate systems is described in section [](#s:floor). 

The `branch reference curve` is the {math}`x = y = 0` curve of the curvilinear coordinate system. 
The branch reference curve does not have to be continuous and, in particular, the coordinate
system through a `Patch` element will generally be discontinuous. If there are no bends with a finite
`tilt_ref` [](#s:bend), and if the beginning element in the branch does not have any orientation
shifts, the branch reference curve will be in the {math}`(x,z)` plane with {math}`y = 0`. Since 
most machines are essentially horizontal, the {math}`x` coordinate is typically thought of as the
"horizontal" coordinate and the {math}`y` coordinate is typically thought of as the "vertical"
coordinate.
 
The "nominal" position of a lattice element is the position of the element without any
[position and orientation shifts](#s:align.g)
(which are sometimes referred to as "misalignments"). 
Each lattice element has "`element body`"
coordinates which are attached to the physical element, and the electric and magnetic
fields of an element are described with respect to `body` coordinates.  
If an element has no
alignment shifts, the `body` coordinates of the element are aligned with the 
`branch` coordinates.
The transformation between `branch` and `body` coordinates is given in
[xxx](#s:lab.body.transform).

%---------------------------------------------------------------------------------------------------
(s:ent.exi)=
## Element Entrance and Exit Coordinates

```{figure} figures/ele-coord-frame.svg
:width: 70%
:name: f:ele.coord.frame

Lattice elements can be imagined as "LEGO blocks" which
fit together to form the branch coordinate system. How elements
join together is determined in part by their entrance and exit coordinate frames. A) For
straight line elements the entrance and exit frames are colinear. B) For bend elements, the 
transformation from entrance to exit coordinates is a rotation about the bend center of curvature.
C) For `Patch` and `floor_shift` elements the 
exit frame may be arbitrarily positioned with respect to the entrance frame.
```

%---------------------------------------------------------------------------------------------------

As discussed in the next section, the branch coordinate system is constructed starting with the first
element in a branch and then building up the coordinate system element-by-element.
Most elements have an "`entrance`" and an "`exit`" coordinate frame as
illustrated in the above figure.
These coordinate frames are attached to the element and are part of the `element body coordinates`. 
`Fiducial` elements ([xxx](#s:fiducial)) are an excption. 
`Fiducial` elements only have a single coordinate frame that is tied to floor coordinates 
and construction of the branch coordinate system starts at this coordinate system. 
See [xxx](#s:fiducial) for more details.
Note that `Girder` elements ([xxx](#s:girder)) also only have a single coordinate frame but they
are not included in any branch.

Most element types have a "straight" geometry as shown in
{numref}`f:ele.coord.frame`A. That is, the reference curve through the element is a straight line
segment with the {math}`x` and {math}`y` axes always pointing in the same direction.
For a [Bend](#s:bend}) element the reference curve is a segment of a circular arc as shown in
{numref}`f:ele.coord.frame`B. With the `ref_tilt` parameter of a bend set to zero, the rotation axis
between the entrance and exit frames is parallel to the {math}`y`-axis ([xxx](#s:floor})).
For [Patch](#s:patch}) and [floor_shift](#s:floorshift)
elements ({numref}`f:ele.coord.frame`C), the exit face can be
arbitrarily oriented with respect to the entrance end.
For `FloorShift` elements the interior reference curve between the
entrance and exit faces is not defined. For the `Patch` element, the interior reference curve 
is dependent upon certain `Patch` element parameter settings ([xxx](#s:patch)) and, in general,
will have a discontinuity.

%---------------------------------------------------------------------------------------------------
(s:ref.construct)=
## Branch Coordinates Construction

%---------------------------------------------------------------------------------------------------

```{figure} figures/patch-between.svg
:width: 70%
:name: f:patch.between

A) The branch coordinates are constructed by
connecting the `downstream` reference frame of one element with the `upstream` reference frame
of the next element in the branch. Coordinates shown are for the mating of element `A` to element
`B`.  B) Example with drift element `dft1` followed by a bend `bnd1`. Both elements have normal
(unreversed) orientation. 
C) Similar to (B) but in this case element `bnd1` is reversed.  D) Similar to (C) but
in this case a reflection patch `P` has been added in between `dft1` and `bnd1`.
In (B), (C), and (D) the {math}`(x,z)` coordinates are drawn at the `entrance` end of the elements. 
The {math}`y` coordinate is always out of the page for this example.
```

%---------------------------------------------------------------------------------------------------

Assuming for the moment that there are no [Fiducial](#s:fiducial) elements present,
the construction of a branch coordinate system starts at the [BeginningEle](#s:begin.ele) element 
at the start of a branch. 
If the branch is a [root](#s:lattice.def) branch, the orientation of the beginning
element within the [floor coordinate system](#s:coords) can be fixed by setting 
[FloorOrientation](#s:orientationition.g) parameters in the `BeginningEle` element.
If the branch is not a `root` branch, the position
of the beginning element is determined by the position of the `Fork` element
from which the branch forks from. The default value of {math}`s` at the `BeginningEle` element is zero
for both root and non-root branches.

Once the beginning element in a branch is positioned, succeeding elements are concatenated together
to form the branch coordinate system. All elements have an "`upstream`" and a "`downstream`"
end as shown in {numref}`f:patch.between`A. 
The `downstream` end of an element is always farther (at greater {math}`s`-position) 
from the beginning element than the `upstream` end of the element. Normally, particles will travel
in the {math}`+s` direction, so particles will enter an element at the upstream end and exit at the
downstream end.

If there are `Fiducial` elements, the branch coordinates are constructed beginning at these
elements working both forwards and backwards along the branch. 
If there are multiple `Fiducial` elements in a branch, there must be a flexible [Patch](#s:patch)
element between them.

If an element is not [reversed](#s:ele.reverse),
the element's `upstream` end is the same as the element's `entrance` end 
({numref}`f:ele.coord.frame`) and the `downstream` end is the same 
as the element's `exit`. If the element is reversed, the `entrance` and `exit` ends are switched.
That is, for a `reversed` element, particles traveling downstream will
enter at the element's `exit` end and will exit at the `entrance` end.

The procedure to connect elements together to form the branch coordinates is to ignore 
`element body` alignment shifts and mate the `downstream` reference frame of
the element with the `upstream` reference frame of the next element in
the branch so that the {math}`(x,y,z)` coordinate axes coincide.
If there are body alignment shifts, the `entrance` and `exit` frames will move with the element. 
However, this does not affect the branch coordinate system.
This is illustrated in {numref}`f:patch.between`. {numref}`f:patch.between`A shows the general situation
with the downstream frame of element `A` mated to the upstream frame of element `B`.
The {math}`(x,z)` coordinates are drawn at the entrance end of the elements and {math}`z` will 
always point towards the element's exit end.
{numref}`f:patch.between`B shows a line
with an normal (unreversed) orientation drift named `dft1` connected to a normal (unreversed) bend named
`bnd1`. {numref}`f:patch.between`C shows the same line but with `bnd1` reversed.
This gives an unphysical situation since a
particle traveling through `dft1` will "fall off" when it gets to the drift's end.
{numref}`f:patch.between`D shows the same line as in {numref}`f:patch.between`C with the addition
of a [`reflection patch`](#s:reflect.patch) `P` between `dft1` and `bnd1` to give a plausible geometry. 
In this case, the patch rotates the coordinate system around the {math}`y`-axis by 180{math}`^o` 
(in this example leaving the {math}`y`-axis invariant). This illustrates why
a reflection patch is always needed between normal and reversed elements.

Notes:
- Irrespective of whether elements are reversed or not, the branch {math}`(x,y,z)` coordinate system
at all {math}`s`-positions will always be a right-handed coordinate system.

- Care must be take when using reversed elements. For example, if the field of the `bnd1` element in
the above example is appropriate for, say, electrons, that is, electrons will be bent in a clockwise
fashion going through `bnd1`, an electron going in a forward direction through the
line in {numref}`f:patch.between`D will be lost in the bend
(the {math}`y`-axis and hence the field is in the same direction for both cases so electrons 
will still be bent in a clockwise fashion but with {numref}`f:patch.between`D a particle needs to be 
bent counterclockwise to get through the bend). 
That is, to get a particle going forward through the bend in {numref}`f:patch.between`D, positrons must be used.

- A `reflection Patch` that rotated the coordinates, for example, 
around the {math}`x`-axis by 180{math}`^o`  would also produce a plausible geometry.
\end{itemize}

%---------------------------------------------------------------------------------------------------
(s:floor)=
## Floor Coordinates

```{figure} figures/floor-coords.svg
:width: 80%
:name: f:floor.coords

The branch coordinate system (purple), which is a function of {math}`s` along the branch reference
curve, is described in the floor coordinate system (black) by a position {math}`(X(s), Y(s), Z(s))` and
and by angles {math}`\theta(s)`, {math}`\phi(s)`, and {math}`\psi(s)`.
```

The Cartesian `floor` coordinate system is the
coordinate system "attached to the earth" that is used to describe the branch coordinate
system. Following the \mad\ convention, the `floor` coordinate axis are labeled {math}`(X, Y,
Z)`. Conventionally, {math}`Y` is the "vertical" coordinate and {math}`(X, Z)` are the "horizontal"
coordinates. To describe how the branch coordinate system is oriented within the floor coordinate
system, each point on the {math}`s`-axis of the branch coordinate system is characterized by its 
{math}`(X, Y, Z)` position and by three angles {math}`\theta(s)`, {math}`\phi(s)`, and {math}`\psi(s)`
that describe the orientation of the branch coordinate axes as shown in {numref}`f:floor.coords`.
These three angles are defined as follows:

- **{math}`\theta(s)` Azimuth (yaw) angle:**
Angle in the {math}`(X, Z)` plane between the {math}`Z`--axis and the projection of the 
{math}`z`--axis onto the {math}`(X, Z)` plane.
A positive angle of
{math}`\theta = \pi/2` corresponds to the projected {math}`z`--axis pointing in the negative 
{math}`X`-direction.

- **{math}`\phi(s)` Pitch (elevation) angle:**
Angle between the {math}`z`-axis and the {math}`(X,Z)` plane. 
A positive angle of {math}`\phi = \pi/2` corresponds to the {math}`z`--axis pointing in the
positive {math}`Y` direction.
%
- **{math}`\psi(s)` Roll angle:**
Angle of the {math}`x`--axis with respect to the line formed by the intersection of the 
{math}`(X, Z)` plane with the {math}`(x, y)` plane.
A positive {math}`\psi` forms a right--handed screw with the {math}`z`--axis.

By default, at {math}`s = 0`, the branch reference curve's origin coincides with the {math}`(X, Y, Z)` 
origin and the {math}`x`, {math}`y`, and {math}`z` axes correspond to the 
{math}`X`, {math}`Y`, and {math}`Z` axes respectively. If the lattice has no
vertical bends (the [ref_tilt](#s:bend) parameter of all bends are zero), the {math}`y`--axis
will always be in the vertical {math}`Y` direction and the {math}`x`--axis will lie in the 
horizontal {math}`(X,Z)` plane.
In this case, {math}`\theta` decreases as one follows the branch reference curve when going through a
horizontal bend with a positive bending angle. This corresponds to {math}`x` pointing radially
outward. Without any vertical bends, the {math}`Y` and {math}`y` axes will coincide, and {math}`\phi` 
and {math}`\psi` will both be zero. 
Parameters of the [BeginningEle](#s:beginning}) element can be used to override these defaults.

Following MAD, the floor position of an element is characterized by a vector {math}`\bf V`
```{math}
  {\bf V} = 
  \begin{pmatrix}
    X \\ Y \\ Z 
  \end{pmatrix}
```
 
The orientation of an element is described by a unitary rotation matrix {math}`\bf W`. 
The column vectors of {math}`\bf W` are the unit vectors spanning the branch coordinate axes in
the order {math}`(x, y, z)`. {math}`\bf W` can be expressed in terms of the
orientation angles {math}`\theta`, {math}`\phi`, and {math}`\psi` via the formula
```{math}
:label: www
  {\bf W} &= {\bf R}_{y}(\theta) \; {\bf R}_{x}(-\phi) \; {\bf R}_{z}(\psi) \\
  &= \begin{pmatrix}
    \cos\theta \cos\psi - \sin\theta \sin\phi \sin\psi & 
        -\cos\theta \sin\psi - \sin\theta \sin\phi \cos\psi & 
         \sin\theta \cos\phi \\
    \cos\phi \sin\psi & \cos\phi \cos\psi & \sin\phi \\
   -\cos\theta \sin\phi \sin\psi - \sin\theta \cos\psi & 
         \sin\theta \sin\psi - \cos\theta \sin\phi \cos\psi & 
         \cos\theta \cos\phi 
  \end{pmatrix}
```
where
```{math}
:label: wtt0t
  {\bf R}_{y}(\theta) &= 
  \begin{pmatrix}
    \cos\theta  & 0 & \sin\theta \\
    0           & 1 & 0          \\
    -\sin\theta & 0 & \cos\theta 
  \end{pmatrix}, \\
  {\bf R}_{x}(\phi) &= 
  \begin{pmatrix}
    1 & 0 & 0                \\
    0 & \cos\phi & -\sin\phi \\
    0 & \sin\phi &  \cos\phi 
  \end{pmatrix}, \\
  {\bf R}_{z}(\psi) &= 
  \begin{pmatrix}
    \cos\psi & -\sin\psi & 0 \\
    \sin\psi &  \cos\psi & 0 \\
    0        &  0        & 1                
  \end{pmatrix}
```
Notice that these are Tait-Bryan angles and not Euler angles.

An alternative representation of the {math}`\bf W` matrix (or any other rotation matrix) is to specify the
axis {math}`\bf u` (normalized to 1) and angle of rotation {math}`\beta`
```{math}
:label: wctux2
  {\bf W} = \begin{pmatrix}
    \cos \beta + u_x^2 \left(1 - \cos \beta \right) & 
    u_x \, u_y \left(1 - \cos \beta \right) - u_z \sin \beta & 
    u_x \, u_z \left(1 - \cos \beta \right) + u_y \sin \beta \\ 
    u_y \, u_x \left(1 - \cos \beta \right) + u_z \sin \beta & 
    \cos \beta + u_y^2\left(1 - \cos \beta \right) & 
    u_y \, u_z \left(1 - \cos \beta \right) - u_x \sin \beta \\ 
    u_z \, u_x \left(1 - \cos \beta \right) - u_y \sin \beta & 
    u_z \, u_y \left(1 - \cos \beta \right) + u_x \sin \beta & 
    \cos \beta + u_z^2\left(1 - \cos \beta \right)
  \end{pmatrix}
```

%---------------------------------------------------------------------------------------------------
(s:ele.pos)=
### Lattice Element Positioning

The lattice standard, again following MAD, computes {math}`\bf V` and {math}`\bf W` 
by starting at the first element of the lattice and iteratively using the equations
```{math}
:label: wws
\begin{align}
  {\bf V}_i &= {\bf W}_{i-1} \; {\bf L}_i + {\bf V}_{i-1}, 
    \\
  {\bf W}_i &= {\bf W}_{i-1} \; {\bf S}_i
\end{align}
```
{math}`{\bf L}_i` is the displacement vector for the {math}`i^{th}` element and matrix 
{math}`{\bf S}_i` is the rotation of
the branch coordinate system of the exit end with respect to the entrance end. For clarity, the
subscript {math}`i` in the equations below will be dripped. For all elements whose reference curve 
through them is a straight line, the corresponding {math}`\bf L` and {math}`\bf S` are
```{math}
:label: l00l
  {\bf L} = 
  \begin{pmatrix}
      0 \\ 0 \\ L
  \end{pmatrix},
  \quad
  {\bf S} = 
  \begin{pmatrix}
      1 & 0 & 0 \\ 
      0 & 1 & 0 \\
      0 & 0 & 1
  \end{pmatrix},
```
Where {math}`L` is the length of the element. 

%-----------------------------------------------------------------------

```{figure} figures/tilt-bend.svg
:width: 80%
:name: f:tilt.bend

A) Rotation axes (bold arrows) for four different `ref_tilt` angles of {math}`\theta_t = 0`, 
{math}`\pm \pi/2`, and {math}`\pi`. 
{math}`(x_0, y_0, z_0)` are the branch coordinates at the entrance end of the bend with
the {math}`z_0` axis being directed into the page. Any rotation axis will be displaced by a distance of
the bend radius `rho` from the origin. B) The {math}`(x, y, z)` coordinates at the exit end of the bend
for the same four `ref_tilt` angles. In this case the bend angle is taken to be {math}`\pi/2`.
```

%-----------------------------------------------------------------------

For a `bend`, the axis of rotation is dependent upon the bend's [`ref_tilt`](#s:offset) angle
as shown in {numref}`f:tilt.bend`A. The axis of rotation points in the negative {math}`y_0`
direction for `ref_tilt` = 0 and is offset by the bend radius `rho`. Here {math}`(x_0, y_0, z_0)`
are the branch coordinates at the entrance end of the bend with the {math}`z_0` axis being directed into
the page in the figure.  For a non-zero `ref_tilt`, the rotation axis is itself rotated about the
`z_0` axis by the value of `ref_tilt`. {numref}`f:tilt.bend`B shows the exit coordinates for four
different values of `ref_tilt` and for a bend angle `angle` of {math}`\pi/2`.  Notice that for a
bend in the horizontal {math}`X-Z` plane, a positive bend `angle` will result in a decreasing azimuth
angle {math}`\theta`.

For a bend, {math}`\bf S` is given using Eq. [](#wctux2) with 
```{math}
:label: ustt
\begin{align}
  {\bf u} &= (-\sin\theta_t, -\cos\theta_t, 0) \\
  \beta &= \alpha_b
\end{align}
```
where {math}`\theta_t` is the `ref_tilt` angle. The {math}`\bf L` vector for a `bend` is given by 
```{math}
:label: lrztt
  {\bf L} = {\bf R}_{z}(\theta_t) \; {\bf \tilde L}, \quad
  {\bf \tilde L} = 
  \begin{pmatrix}
    \rho (\cos\alpha_b - 1) \\ 0 \\ \rho \, \sin\alpha_b
  \end{pmatrix}
```
where {math}`\alpha_b` is the bend [angle](#s:bend) and {math}`\rho` being the bend radius
({math}`\rho`). Notice that since {math}`\bf u` is perpendicular to {math}`z`, the curvilinear reference coordinate
system has no "torsion". Note: The branch coordinate system can be related to a Frenet-Serret coordinate system, but 
the two coordinate systems do not coincide.
Frenet-Serret coordinates use the radial direction in a bend as a coordinate axis while
with branch coordinates the radial direction can point anywhere in the {math}`(x,y)` plane.

Note: An alternative equation for {math}`\bf S` for a bend is
```{math}
:label: srrr
  {\bf S} = {\bf R}_{z}(\theta_t) \; {\bf R}_{y}(-\alpha_b) \; {\bf R}_{z}(-\theta_t)
```
The bend transformation above is so constructed that the transformation is equivalent to rotating
the branch coordinate system around an axis that is perpendicular to the plane of the bend. This
rotation axis is invariant under the bend transformation. 
For example, for {math}`\theta_t = 0` (or {math}`\pi`) the {math}`y`-axis is
the rotation axis and the {math}`y`-axis of the branch coordinates before the bend will be
parallel to the {math}`y`-axis of the branch coordinates after the bend as shown in {numref}`f:tilt.bend`. 
That is, a lattice with only bends with {math}`\theta_t = 0` or {math}`\pi` will lie in the 
horizontal plane (this assuming that the {math}`y`-axis starts out pointing along 
the {math}`Y`-axis as it does by default).
For {math}`\theta_t = \pm\pi/2`, the bend axis is the {math}`x`-axis. 
A value of {math}`\theta_t = +\pi/2` represents a downward pointing bend.

