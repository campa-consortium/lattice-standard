# About the Lattice Standard

## What the Lattice Standard is

1. The Lattice Standard is an *abstract* description of a lattice for the simulated transport of charged particles.

1. A Lattice is an ordered list of regions in space distinguished by the presense of (possibly time-varying) specially described electromagnetic fields,
materials, apertures and other possible engineered structures. For simplicity, we will denote these regions as _lattice_elements_ although a region
may be distinguished by the absence of material (drift space.)

1. A lattice will have the notion of a _reference_trajectory_ which is the defined path of the particle which the lattice is intended to transport.

1. The positioning of lattice elements will be described by their order in the list of elements and their $s$ coordinate, the distance along the path of
propagated reference particle. The definition and use of coordinates transverse to the direction of motion is not in this document  and will be
the subject of the definition of the standard.

1. The Lattice Standard is intended to be extensible and general enough to accommodate elements that are used in current particle transport machines.

1. The way in which elements are described is not part of this document and will make up the main part of the work of the standard.

1. The Lattice Standard defines a language and a document composed of words and symbols made of UTF-8 characters that is human readable.

1. The language defined by the Lattice Standard should conform to a formal grammar in the the common meaning in computer science and be unambiguously parsable by machines.

1. Any particular simulation code may implement transport of particles through a particular element type as described in the Lattice Standard. In that case, that element type will be said to be implemented by that particular code.

1. Any particular simulation code may not implement transport of particles through a particular element type as described in the Lattice Standard. In that case, that element type will be said to be not implemented by that particular code.

1. Any code may make its own choice about what parts and what elements in the Lattice Standard it chooses to implement.

### Ingredients for the Lattice Standard

1. It would be nice for the Lattice Standard language to have the ability to define subsections of a machine (arcs) which substitutable arguments that can be duplicated and placed within a larger structure.

1. It would be nice for the Lattice Standard language to allow mechanisms to manipulate and transform defined arcs.

1. It would be nice for the Lattice Standard language to allow lattice element properties to be specified with substituble parameters
which may also be mathematical expressions.

## What the Lattice Standard is not

1. The Lattice Standard does not say anything about its implementation in any computer language.

1. The Lattice Standard does not say anything about the algorithms or physics that goes into the implmentation of particle transport simulations.

1. Any particular lattice element may include in its description hints about the way in which it is intended to be implemented. The standard does not require their usage in any code in any particular way.

## Lattice Readers

1. The Lattice Standard organization encourages the development of software in various languages that implement tools
for parsing Lattice Standard documents and making the information available to particle transport codes. These tools are separate from the Lattice Standard.

1. At a minimum, a Reader will parse a Lattice Standard document and provide and interface for a code to request the list of elements with their characteristics as defined in the document in the
form which is appropriate for the language in which is implemented. The standard does not prescribe the way that this is done.

1. If a reader encounters a document purporting to be a Lattice Standard document that it is unable to parse, it may choose its own action among possibilities such as
    1. Fail
    2. Parse the elements that it can
    3. Attempt corrective actions

1. Each reader will define its own interface in its own documentation.


