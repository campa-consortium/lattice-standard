(c:construction)=
# Constructing Lattices

%---------------------------------------------------------------------------------------------------
(s:beamlines)=
## BeamLines

A lattice [`branch`](#s:branches) is constructed from a `BeamLine`. A BeamLine is essentially 
an ordered array of elements. Each element is either a lattice element or a `BeamLine`. 
A BeamLine that is contained within another BeamLine is called a `subline`
of the containing BeamLine.
The top level `BeamLine` from which a branch is constructed is called a `root Beamline`. 

The components of a BeamLine are:
```{code} yaml
name        # String: Name of the BeamLine. Optional.
multipass   # Bool: Multipass line or not. Optional. Default is False.
length      # [m]: Length of the BeamLine. Optional.
line        # Ordered list: List of elements. Required.
reference_point  # String: Name of a line item used as a reference. Optional.
```

The `name` component is a string that can be used to reference the `BeamLine`.

The optional `mutipass` component is a boolean describing whether the `BeamLine` is part of 
a [`multipass`](#c:multipass) construct. The default is False.

The optional `length` component gives the length of the `BeamLine`. 
If `length` is not given, the BeamLine ends at the downstream end of the final
element in the `line` and with this the length of the BeamLine can be calculated.

The optional `reference_point` component is used to position sublines.
The value of `reference_point` is the name of a `line item` that marks the reference point. 
To make things unambiguous, the reference `item` must have zero length.
In most cases, this means that the `reference_point` cannot be a `BeamLine`.

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
  reference_point: thingC
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
a reference line item when there is an explicit placement component present.
```

If the longitudinal placement of a line `item` is not specified, as is the case with the above
examples, a line `item` is placed so the upstream end of the item is flush with the downstream end
of the preceding item as explained in the [Branch Coordinates Construction](#s:ref.construct) section.

To adjust the longitudinal placement of an `item`, 
the `placement` component of an `item` can be used.
When there is a `placement` component, figure {numref}`f:superposition` shows how the line `item` 
is positioned with respect to a reference line `item`. 
If the reference `item` is not specified, the beginning of the `line` is used. In this case, 
a `reference_origin` may not be specified.

The components of `placement` are:
```{code} yaml
offset            # Real [m]. Longitudinal offset of the line item.
origin            # Switch. Line item origin point.
reference         # String. Reference line item.
reference_origin  # Switch. Reference line item origin point.
```

The `reference_origin` is the reference point on the reference line element and `origin` is the
reference point on the element being positioned. These switches may take the values:
```{code} yaml
ENTRANCE_END       # Entrance end of the `item`.
CENTER             # Center of the `item`.
EXIT_END           # Exit end of the `item`.
REFERENCE_POINT    # Used with sublines that define a reference point.         
```

Example:
```{code} yaml
BeamLine:
  line:
    - item: thingA
    - item:
        name: this_line
        placement:
          offset = 37.5
          reference: thingA
          reference_origin: EXIT_END
          origin: REFERENCE_POINT
        ...
    ...
```
In this example, the origin point of `this_line`, which is the `reference_point` of `this_line`,
is placed `37.5` meters from the origin point of `thingA`. The origin point of `thingA` being
the exit end of `thingA`. 

To make placement unambiguous, A `reference` `item` must appear before the `item` being placed.
In a section of a line where the lattice elements are not reversed, a positive `offset` moves
the element being placed downstream. If there is reversal, a positive `offset` moves
the element being placed upstream. That is, placement will not affect the relative positions
of items if a line is reversed.

Note: Lattice elements are allowed to overlap but it should be kept in mind that 
some programs will not be able to handle overlapping fields.

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
      geometry: CLOSED
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
geometry    # Switch: OPEN or CLOSED. Default is OPEN.
```
The optional `geometry` component of a `BeamLine` is a switch with possible settings
```{code} yaml
OPEN          # Default
CLOSED
```
A `CLOSED` geometry is used to indicate that the `Branch` is something like a storage ring where the
particle beam recirculates through the `Branch` multiple times.
An `OPEN` geometry is used to indicate that the `Branch` is something like a Linac or any other line
that is "single pass". 

Notice that by specifying a `CLOSED` geometry it does **not** mean that the downstream end of
the last element of the `Branch` has the same [floor](#s:floor) coordinates as the floor
coordinates at the beginning. Setting the geometry to `CLOSED` simply signals to a program that
it may be appropriate to calculate closed (periodic) orbits and periodic Twiss parameters
as opposed to calculating orbits and Twiss
parameters based upon initial orbit and Twiss parameters set for the beginning of the `Branch`.  
Indeed, it is sometimes convenient to treat branches as closed even though there is no 
closure in the floor coordinate sense.
For example, when a storage ring has a number of repeating "periods", it may be
convenient to only use one period in a simulation. 

% Stuff that could be added to the standard:
%  BeamLine manipulations like slices, element removal, substitution list, etc.