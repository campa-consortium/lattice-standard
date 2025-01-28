# Introduction

<<<<<<< HEAD:source/standard/introduction.md
## Overview

The Particle Accelerator Lattice Standard (PALS) defines a standard for the sharing of lattice information to describe
particle accelerators and storage rings. PALS aims to promote:

 - portability between various applications and differing algorithms
 - a unified open access description for scientific data (publishing and archiving)
 - a unified description for post-processing, visualization and analysis.

PALS is able to describe the connections between various things
from the connection of injection and extraction lines connected to a storage ring to the interaction region
of colliding beam storage rings where particles are moving through magnets in opposite directions. A PALS
based lattice is able to
hold all the information about an entire machine complex from beam creation to dump lines enabling a 
single lattice to be used as the basis of start-to-end simulations.

PALS is built to be easily customizable so that custom information may be inserted by a program into a lattice.
This custom information is generally not usable by other programs but can be useful when a program accesses
lattice files that it generated. 


## What PALS Is

PALS is a schema that defines things like the names of various lattice element types, how to organize lattice
elements into lines which beams of particles or X-rays can move through, etc. 

## What PALS Is Not

PALS does not define any particular grammar to implement the PALS schema. Rather, there are associated
language specific standards that define grammars for YAML, JSON, Python, etc. Along with these
associated standards, there are packages that implement translation between lattice files and a representational
internal format defined by the package.

PALS does not define how particles are to be tracked through a lattice. PALS is for describing machines and
not for defining how to simulate particle motion. 

=======
>>>>>>> d632946b33fa799d412f8115611bc34434214c1a:source/introduction.md
## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

All `keywords` in this standard are case-sensitive.

## Lattice Elements

The basic building block used to describe an accelerator is the lattice \vn{element}. Typically,
a lattice element is something physical like a bending magnet or an electrostatic
quadrupole, or a diffracting crystal. A lattice element may define a region in space 
distinguished by the presence of (possibly time-varying) electromagnetic fields,
materials, apertures and other possible engineered structures. However, lattice elements
are not restricted to being something physical and may, for example, just mark a particular point in space
(EG: **Marker** elements), or may designate where beam lines intersect (**Fork** elements).
By convention, element names in PALS will be upper camel case.


## Lattice Branches

A lattice **branch** holds a collection of lattice elements. 
There are two types of branches. One type, called a "tracking branch", holds
an ordered array of lattice elements that gives a
sequence of elements to be tracked through. A tracking branch can represent something like a
storage ring, transfer line or Linac.
In the simplist case, a program can track through the elements one element at a time.
However, lattice elements may overlap which will naturally complicate tracking.

The other type of branch, called a "lord" branch, is used to hold lattice elements that help describe:
- Support elements (Girders)
- Overlapping elements (Superposition)
- Situations where an element is transversed multiple times as in an ERL or in opposite directions
as in a colliding beam machine (Multipass)
Lord branches will be explained in detail in later sections.

## Lattices

A **lattice** is the root structure holding the information about a
``machine``. A machine may be as simple as a line of elements (like the elements of a Linac) or
as complicated as an entire accelerator complex with multiple storage rings, Linacs, transfer
lines, etc.

Essentially, a **lattice**, has an array of **branches** with each branch describing part of the
machine. Branches can be interconnected to form a unified whole.
Branches can be interconnected using **Fork** elements. 
This is used to simulate forking beam lines such as a connections to a transfer line, dump line, or an
X-ray beam line. The **branch** from which other **branches** fork but is not forked to by any
other branch is called a **root** branch.

A lattice may contain multiple **root** branches. For example, a pair of intersecting storage
rings will generally have two root branches, one for each ring.

## Syntax Used in this Document

ALS does not define any particular language to implement the ALS schema. Rather, there are associated
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
  x_limit: [-0.03, 0.03]
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
The first represents an unordered dictionary of key, value pairs and the second represents an ordered 
dictionary.

## Names

Many constructs in the standard like lattice elements, branches, parameter groups, etc may have
an associated name. To ensure seamless translation to particular languages, all names must conform
to the following:
- A name must start with a letter or the underscore character
- A name cannot start with a number
- A name can only contain alpha-numeric characters and underscores (A-z, 0-9, and _ )

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