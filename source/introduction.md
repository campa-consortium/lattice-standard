(c:introduction)=
# Introduction

%---------------------------------------------------------------------------------------------------
(s:conventions)=
## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

All `keywords` in this standard are case-sensitive.

%---------------------------------------------------------------------------------------------------
(s:elements)=
## Lattice Elements

The basic building block used to describe an accelerator is the lattice \vn{element}. Typically,
a lattice element is something physical like a bending magnet or an electrostatic
quadrupole, or a diffracting crystal. A lattice element may define a region in space 
distinguished by the presence of (possibly time-varying) electromagnetic fields,
materials, apertures and other possible engineered structures. However, lattice elements
are not restricted to being something physical and may, for example, just mark a particular point in space
(EG: `Marker` elements), or may designate where beamlines intersect (`Fork` elements).
By convention, element names in PALS will be upper camel case.

%---------------------------------------------------------------------------------------------------
(s:branches)=
## Lattice Branches

A lattice `branch` holds an ordered array of lattice elements
that gives the sequence of elements to be tracked through. 
A branch can represent something like a storage ring, transfer line or Linac.
In the simplest case, a program can track through the elements one element at a time.
However, lattice elements may overlap which will naturally complicate tracking.

%---------------------------------------------------------------------------------------------------
(s:lattices)=
## Lattices

A `lattice` is the root structure holding the information about a
``machine``. A machine may be as simple as a line of elements (like the elements of a Linac) or
as complicated as an entire accelerator complex with multiple storage rings, Linacs, transfer
lines, etc.

Essentially a `lattice` has an array of `branches` with each branch describing part of the
machine. Branches can be interconnected to form a unified whole.
Branches can be interconnected using `Fork` elements. 
This is used to simulate forking beamlines such as a connections to a transfer line, dump line, or an
X-ray beamline.

Besides `branches`, a lattice will hold information like details of any support girders that are
present and `multipass` information in the case where elements are transversed multiple times 
as in an ERL or in opposite directions as in a colliding beam machine.

%---------------------------------------------------------------------------------------------------
(s:root)=
## Root branch

The `branch` from which other `branches` fork but is not forked to by any
other branch is called a `root` branch.
A lattice may contain multiple `root` branches. For example, a pair of intersecting storage
rings will generally have two root branches, one for each ring.

%---------------------------------------------------------------------------------------------------
(s:expansion)=
## Lattice Expansion

An important concept is `lattice expansion`, which can also be called `branch expansion` or
`beamline expansion`. Lattice expansion is the process, starting from the `root` `BeamLine`
of a branch, of constructing the ordered list of lattice elements contained in that branch.

%---------------------------------------------------------------------------------------------------
(s:syntax)=
## Syntax Used in this Document

PALS does not define any particular language to implement the PALS schema. Rather, there are associated
language specific standards that define grammars for YAML, JSON, Python, etc. Along with these
associated standards, there are packages that implement translation between lattice files and a representational
internal format defined by the package.

While the standard itself is language agnostic, this document that describes the standard
needs to use some syntax and this syntax is based upon YAML. Non-YAML syntax used here is:

1. The {math}`N^{th}` item in a list is referred to using square brackets enclosing the index: `[N]`.
For example:
```{code} YAML
Aperture:
  name: ap1
  x_limit: [-0.03, 0.04]
```
here `x_limit[1]` and `x_limit[2]` would refer to the first and second values of `x_limit` respectively.

2. The standard defines the following symbols which can be used in place of a value: 
- `Inf`    # Infinity
- `-Inf`   # Negative infinity
- `NaN`    # Not a number

Note: There is a difference between
```{code} yaml
this_group:
  key1: value1
  key2: value2
  key3: value3
```
and
```{code} yaml
this_group:
  - key1: value1
  - key2: value2
  - key3: value3
```
The first represents an unordered dictionary of key-value pairs and the second represents an ordered 
dictionary of key-value pairs.

%---------------------------------------------------------------------------------------------------
(s:names)=
## Names

Many constructs in the standard like lattice elements, branches, parameter groups, etc., may have
an associated name. To ensure seamless translation to particular languages, all names must conform
to the following:
- A name must start with a letter or the underscore character
- A name cannot start with a number
- A name can only contain alpha-numeric characters and underscores (A-Z, a-z, 0-9, and _ )

%---------------------------------------------------------------------------------------------------
(s:units)=
## Units

The lattice standard uses SI except for energy which uses `eV`.
```{list-table} Units used by the Standard
:width: 60%
:header-rows: 1

* - Quantity
  - Units
* - Length
  - meters
* - time
  - seconds
* - energy
  - eV
* - momentum
  - eV/c
* - mass
  - eV/c^2
* - Voltage
  - Volts
* - angles and phases
  - radians / 2 {math}`\pi`
* - Magnetic field
  - Tesla
* - frequency
  - Hz
* - Electric field
  - Volts/m
```