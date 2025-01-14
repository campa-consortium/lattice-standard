# Introduction

## Overview

The Accelerator Lattice Standard (ALS) defines a standard for the sharing of lattice information to describe
particle accelerators and storage rings. ALS aims to promote:

 - portability between various applications and differing algorithms
 - a unified open access description for scientific data (publishing and archiving)
 - a unified description for post-processing, visualization and analysis.

ALS is able to describe the connections between various things
from the connection of injection and extraction lines connected to a storage ring to the interaction region
of colliding beam storage rings where particles are moving through magnets in opposite directions. An ALS
based lattice is able to
hold all the information about an entire machine complex from beam creation to dump lines enabling a 
single lattice to be used as the basis of start-to-end simulations.

ALS is built to be easily customizable so that custom information may be inserted by a program into a lattice.
This custom information is generally not usable by other programs but can be useful when a program accesses
lattice files that it generated. 


## What ALS Is

ALS is a schema that defines things like the names of various lattice element types, how to organize lattice
elements into lines which beams of particles or X-rays can move through, etc. 

## What ALS Is Not

ALS does not define any particular grammar to implement the ALS schema. Rather, there are associated
language specific standards that define grammars for YAML, JSON, Python, etc. Along with these
associated standards, there are packages that implement translation between lattice files and a representational
internal format defined by the package.

ALS does not define how particles are to be tracked through a lattice. ALS is for describing machines and
not for defining how to simulate particle motion. 

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
By convention, element names in ALS will be upper camel case.


## Lattice Branches

A lattice **branch** holds a collection of lattice elements. 
There are two types of branches.  One type, called a "tracking branch", holds
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

A **lattice is the root structure holding the information about a
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
