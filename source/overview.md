# Overview

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

PALS does not define how particles are to be tracked through a lattice. PALS is for describing machines and
not for defining how to simulate particle motion. 
