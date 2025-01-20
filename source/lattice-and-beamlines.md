# Constructing Lattices

### Beam line

A BeamLine is essentially an array of elements
```{code} yaml
BeamLine:
  name: inj_line
  multipass: True
  length: 37.8
  line:
    - eleA
    - eleB
    - eleC

## Lattice

A lattice is essentially an array of branches. Each branch is instantiated from a `root` beam line.