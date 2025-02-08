(c:construction)=
# Constructing Lattices

%---------------------------------------------------------------------------------------------------
(s:beamlines)=
## BeamLines

A lattice [`branch`](#s:branches) is constructed from a `BeamLine`. A BeamLine is essentially 
an ordered array of elements. 
Each element of a `BeamLine` is either a lattice element or another `BeamLine`. 
A BeamLine that is contained within another BeamLine is called a `subline`
of the containing BeamLine.
The top level `BeamLine` from which a branch is constructed is called a `root Beamline`. 

The components of a BeamLine are:
```{code} yaml
name        # Optional. String: Name of the BeamLine.
multipass   # Optional. Bool: Multipass line or not. Default is False.
length      # Optional. [m]: Length of the BeamLine.
line        # Ordered list: List of elements.
zero_point  # Optional. String: Name of a line item used as a reference point when
            #  the BeamLine is used as a subline.
```

The `name` component is a string that can be used to reference the `BeamLine`.

The optional `mutipass` component is a boolean describing whether the `BeamLine` is part of 
a [`multipass`](#c:multipass) construct. The default is False.

The optional `length` component gives the length of the `BeamLine`. 
If `length` is not given, the BeamLine ends at the downstream end of the final
element in the `line` and with this the length of the BeamLine can be calculated.

The optional `zero_point` component is used to position sublines.
The value of `zero_point` is the name of a `line item` that marks the reference point. 
To make things unambiguous, the reference `item` must have zero length.
In most cases, this means that the `zero_point` cannot be a `BeamLine`.

The `line` component of a BeamLine holds an ordered list of `items`. 
Each `item` represents one (or more if there is a `repeat` count) lattice element or
BeamLine. 

A line `item` can have a value that is the name of a lattice element or `BeamLine`, 
or the `item` can have a component that is one of
```{code} yaml
name                # Name of lattice element or BeamLine.
Element             # Lattice element definition.
BeamLine            # subline definition.
```
In addition, a line `item` has optional components
```{code} yaml
repetion        # Integer. Repetition count. Default is 1.
direction       # +1 or -1. Longitudinal orientation of element. Default is +1.
placement       # Structure. Shifts element or subline longitudinally.
```

Example:
```{code} yaml
BeamLine:
  name: inj_line
  multipass: True
  length: 37.8
  zero_point: thingC
  line:
    - item: thingB      # Name of an element or BeamLine defined elsewhere.
    - item:             # Another way of specifying the name of an element or BeamLine.
        name: thingC
    - item:             # This item contains an Element that is reversed.
        direction: -1
        Element:
          ...
    - item:             # This item contains a BeamLine repeated three times    
        repetition: 3
        BeamLine:
          ...
```

%---------------------------------------------------------------------------------------------------
(s:line.construction)=
### Constructing a BeamLine `line` 

A line item that is a lattice element can be specified by name if a lattice element
of that name has been defined. Example:
```{code} yaml
Element: 
  name: q1w
  type: Quadrupole
  ...

BeamLine:
  line:
    - item: q1w       # Line item is element q1w.
    - item:           # Line item is element q1w the same as the previous item.
        Element:
            inherit: q1w
    ...
```

A line item which is a lattice element can also be specified by defining the lattice element
"in place" in the line. Example:
```{code} yaml
BeamLine:
  line:
    - item:
        Element:
          name: octA
          type: Octupole
          Kn3L: 0.34
          ...
    ...
```

Similarly, a line item which is a BeamLine can either be referred to by name or can
be defined "in place". Example:
```{code} yaml
BeamLine:
  line:
    - item: inj_line          # Refer by name to a previously defined BeamLine
    - item:
        BeamLine:             # Define a subline in place
          line:
           ...
    ...
```

Restriction: Infinite recursion of sublines is not allowed. 
For example, if BeamLine `B` is a subline of `A`, then BeamLine `A` may not be a subline of `B`.

%---------------------------------------------------------------------------------------------------
(s:repetition)=
### Repetition

For any line `item`, a `repetition` count component can be used to represent multiple copies
of the item. Example:
```{code} yaml
BeamLine:
  name: full_line
  line:
    - item:
        name: short_line
        repetition: 3
```
In this case, `short_line` is repeated three times when the BeamLine is expanded to form a lattice
branch. For example, if `short_line` is defined by:
```{code} yaml
BeamLine:
  name: short_line
  line:
    - item: A
    - item: B
    - item: C
```
then the expanded `full_line` will look like:
```{code} yaml
A, B, C, A, B, C, A, B, C
```

repetition counts can be negative. In this case, the elements are taken to occur in reverse order.
Thus, in the above example, if the `repetition` count was `-3`, the expanded `full_line` will
look like:
```{code} yaml
C, B, A, C, B, A, C, B, A
```

Notice that reverse order does not mean true [direction reversal](#s:ref.construct). 
For elements that have longitudinal symmetry, this does not matter. 
However, for example, for a `Bend` element that is in a line with reversed order,
the edge angle `e1` will still represent the edge of the upstream side and `e2` will represent the edge 
at the downstream side.

%---------------------------------------------------------------------------------------------------
(s:direction)=
### Direction reversal

The optional `direction` component of `item` can be used for true [direction reversal](#s:ref.construct).
Possible values are `+1` and `-1`. The Default is `+1` which represents an unreversed element
or BeamLine. BeamLine reversal involves both reversed order of the line and direction reversal of
the individual line items. Example:
```{code} yaml
BeamLine:
  name: lineA
  line:
    - item: 
        direction: -1
        name: lineB

BeamLine:
  name: lineB
  line:
    - item: ele1
    - item:
        direction: -1
        name: ele2
```
The expanded `lineA` has elements:
```{code} yaml
ele2, -ele1
```
where the negative sign here indicates that `ele1` is reversed in direction. 
Notice that `ele2` is not reversed since the reversal of a reversed element results
in an element that is unreversed.

%---------------------------------------------------------------------------------------------------
(s:placement)=
### Line item placement

```{figure} figures/superimpose.svg
:width: 80%
:name: f:superposition

Positioning of a line item (which may be a lattice element or BeamLine) with respect to 
a "base" line item when there is an explicit placement component present. Assuming
unreversed elements, a positive offset positions the item being positioned downstream
of the `base_item`. The figure is drawn with the `from_point` and `to_point` having their
default values of `EXIT_END` and `ENTRANCE_END` respectively and assuming that the line
items are not reversed in orientation.
```

By default,
a line `item` is placed such that the entrance end of the `item` is flush with the exit end
of the preceding `item` as explained in the [Branch Coordinates Construction](#s:ref.construct) section.
To adjust the longitudinal placement of an `item`, 
the `placement` component of an `item` can be used.
When there is a `placement` component, figure {numref}`f:superposition` shows how the line `item` 
is positioned with respect to a `"base"` line `item`. 

The components of `placement` are:
```{code} yaml
offset       # Optional Real [m]. Longitudinal offset of the line item. Default is zero.
to_point     # Optional switch. Line `item` offset end point. Default is ENTRANCE_END.
base_item    # Optional string. Line `item` containing the `from_point`.  
from_point   # Optional switch. Base line `item` offset beginning point. Default is EXIT_END.
```
If the `base_item` is not specified, the default is the previous element or the beginning 
of the `line` if there is no previous element.

The `from_point` is the reference point on the base line `item` and `to_point` is the
reference point on the element being positioned. The distance between these points is set by 
the value of `offset`.
The values of `from_point` and `to_point` can be one of the following:
```{code} yaml
ENTRANCE_END       # Entrance end of the `item`. Default for the `to_point` component.
CENTER             # Center of the `item`.
EXIT_END           # Exit end of the `item`. Default for the `from_point` component.
ZERO_POINT         # Used with sublines that define a `zero_point`.
```

Example:
```{code} yaml
BeamLine:
  name: position_line
  line:
    - item: thingA
    - item:
        name: this_line
        placement:
          offset = 37.5
          base_item: thingA
          from_point: EXIT_END
          to_point: ZERO_POINT
        ...
    ...
```
In this example, the `to_point` is the `zero_point` of `this_line`.
The `from_point` of `thingA` is placed `37.5` meters from the `to_point` point with
the `to_point` being at the exit end of `thingA`.

The value of `offset` may be negative as well as positive. With negative offsets, 
the lattice expansion calculation may become recursive but, in any case, plancement
must be computable. That is, situations where there in infinite recursion is forbidden.

In a section of a line where the lattice elements are not reversed, a positive `offset` moves
the element being placed downstream. If there is reversal, a positive `offset` moves
the element being placed upstream. That is, placement will not affect the relative distances
of items if a line is reversed. In the above example, if `position_line` expandeds to:
```{code} yaml
thingA, thingB, thingC
```
then the following 
```{code} yaml
BeamLine:
  line:
  - item:
      name: position_line
      repetition: -1
```
Would expand to
```{code} yaml
thingC, thingB, thingA
```
with the same relative distances between elements. Similarly, this:
```{code} yaml
BeamLine:
  line:
  - item:
      name: position_line
      direction: -1
```
Would expand to
```{code} yaml
-thingC, -thingB, -thingA
```
again with the same relative distances between elements.

Lattice elements are allowed to overlap but it should be kept in mind that 
some programs will not be able to handle overlapping fields. 
To remove an ambiguity, if two zero length elements are next to each other in a `line`, the order of the
elements determines the order in which they should tracked through. For example,
if a line contains the two zero length elements:
```{code} yaml
BeamLine:
  line:
  - item: markerA
  - item: markerB
```
then the order of tracking will be `markerA` followed by `markerB`.

%---------------------------------------------------------------------------------------------------
(s:lattice.construct)=
## Constructing a Lattice

A `Lattice` is essentially an array of ordered `Branches`. 
Each branch is instantiated from a `root` `BeamLine`.
Example:
```{code} yaml
Lattice:
  - Branch: this_line
  - Branch: 
      name: that_ring
      periodic: True
```
In this example, `this_line` and `that_ring` are the names of the root BeamLines
for the two `Branches`.

When the root BeamLines are expanded to form the branches, there may be `Fork` elements present
which can either connect to lattice elements that exist in the lattice or may connect to new
BeamLines. In the latter case, after expansion, `Branches` will be added as necessary to connect
the `Fork` elements to. And these added `Branches` may themselves contain `Fork` elements which
can lead to more `Branches` being added. In this way, an entire accelerator complex may be
described by a single `Lattice`. Forking and `Fork` elements are described in detail in
the [Forking](#c:forking) chapter.

A `Branch` has the optional component
```{code} yaml
periodic    # Optional Bool. Default is False.
```
The optional `periodic` component of a `BeamLine` is a switch with possible settings
```{code} yaml
OPEN          # Default
CLOSED
```
Setting `periodic` to `True` is used to indicate that the `Branch` is something like a 
storage ring where the particle beam recirculates through the `Branch` multiple times.
Setting `periodic` to `False` is used to indicate that the `Branch` is something like a 
Linac or any other line that is "single pass". 

Notice that a setting `periodic` to `True` does **not** mean that the downstream end of
the last element of the `Branch` has the same [floor](#s:floor) coordinates as the floor
coordinates at the beginning. Setting `periodic` to `True` simply signals to a program that
it may be appropriate to calculate closed (periodic) orbits and Twiss parameters
as opposed to calculating orbits and Twiss
parameters based upon orbit and Twiss parameters set by the User for the beginning of the `Branch`.  
Indeed, it is sometimes convenient to treat branches as periodic even though there is no 
closure in the floor coordinate sense.
For example, when a storage ring has a number of repeating "periods", it may be
convenient, for speed reasons, to calculate the periodic functions using only use one period.
