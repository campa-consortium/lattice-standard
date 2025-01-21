# Overview

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

ALS does not define how particles are to be tracked through a lattice. ALS is for describing machines and
not for defining how to simulate particle motion. 
