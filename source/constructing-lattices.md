# Constructing Lattices


## BeamLines

A lattice `branch` is constructed from a `BeamLine`. A BeamLine is essentially 
an ordered array of elements. The components of a BeamLine are:
```{code} yaml
name        # String: Name of the BeamLine. Optional.
multipass   # Bool: Multipass line or not? Optional. Default is False.
length      # [m]: Length of the BeamLine. Optional.
line        # Ordered list: List of elements. Required.
reference_element  # Reference element. Optional.
```

If the `length` of the BeamLine is not given, the BeamLine ends at the downstream end of the final
element in the `line` and with this the length of the BeamLine can be calculated.
Simple example:
```{code} yaml
BeamLine:
  name: inj_line
  multipass: True
  length: 37.8
  reference_element: thingB
  line:
    - thingA
    - thingB
    - thingC
```

The `line` component of a BeamLine holds an ordered list of `items`. 
Each line item is either a lattice element or a BeamLine.
Lattice elements are allowed to overlap but it should be noted that this may hinder portability.

The `referece_element` component can be used to position a BeamLine within another BeamLine.
The referece element specified by `reference_element` must have zero length.
See below.

### Constructing a BeamLine `line` 

A line item which is a lattice element can be specified by name if a lattice element
of that name has been defined. Example:
```{code} yaml
Element: 
  name: q1w
  type: Quadrupole
  ...

BeamLine:
  line:
    - q1w             # Line item is element q1w.
    - Element:        # Line item is element q1w the same as above.
        inherit: q1w
    ...
```

A line item which is a lattice element can also be specified by defining the lattice element
"in place" in the line. Example:
```{code} yaml
BeamLine:
  line:
    - Element:
        name: octA
        type: Octupole
        Kn3L = 0.34
        ...
    ...
```

Similarly, a line item which is a BeamLine can either be referred to by name or can
be defined "in place". Example:
```{code} yaml
BeamLine:
  line:
    - BeamLine: inj_line    # Refer by name to a previously defined BeamLine
    - BeamLine:             # Define in place
        name: Amtrack
        line:
           ...
    ...
```

Restriction: Infinite recursion of of BeamLines within BeamLines is not allowed. For example,
If the `line` of BeamLine `A` contains BeamLine `B`, then the `line` of BeamLine `B` may not
contain `A`.

### Repetition and reversal

For any line item, a `repetition` count component can be used to represent multiple 

### Line item placement

If there is no explicit placement of a line item

```{figure} figures/superposition.svg
:width: 80%
:name: f:superposition

Positioning of a line item (which may be a lattice element or BeamLine) with respect to 
a reference line item when there is an explicit placement.
```

If the longitudinal placement of a line item is not specified, as is the case with the above
examples, a line item is placed so the upstream end of the item is flush with the downstream end
of the preceding element as explained in the [Branch Coordinates Construction](#s:ref.construct) section.

Explicit longitudinal placement of a line item uses a `placement` component. Example:
```{code} yaml
BeamLine:
  line:
    - Element: thingA
    - BeamLine:
        inherit: Amtrack
        placement:
          - offset = 37.5
          - reference: thingA
          - reference_origin: DOWNSTREAM_END
          - origin: REFERENCE_ELEMENT
        ...
    ...
```
The components of `placement` are:
```{code} yaml
offset            # Longitudinal offset of the line item.
origin            # Line item origin point.
reference         # Reference line item.
reference_origin  # Reference line item origin point.
```
Figure {refnum}`f:superposition` shows how a line item is positioned with respect to a reference line item. 
If the reference element is not specified, the beginning of the line is used. In this case, 
a `referece_origin` may not be specified.

The `reference_origin` is the reference point on the reference line element and `origin` is the
reference point on the element being positioned. These switches may take the values:
```{code} yaml
UPSTREAM_END
CENTER
DOWNSTREAM_END
REFERENCE_ELEMENT    # Used with BeamLines that define a reference element.         
```


## Constructing a Lattice

A lattice is essentially an array of branches. Each branch is instantiated from a `root` beam line.



%-------------------------------------------
% Note: To be discussed in future PRs: 
% Multipass
% Superposition
% Forking and Fork elements
%
% Stuff that could be added to the standard:
%  BeamLine manipulations like slices, element removal etc.

reversal

reflection
repetition: -1

substitution list