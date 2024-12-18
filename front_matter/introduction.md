# Accelerator Lattice Standard

This standard is an effort to create a standard to promote lattice information exchange for particle accelerators.

## Scope

The standard focuses on the physical layout and properties of a machine.
This excludes description of tracking of particles including collective effects.

## Participation

There are weekly (more or less) meetings to discuss development of the lattice standard.
To participate in the creation of the lattice standard, please contact [Jean-Luc Vay](https://https://github.com/jlvay) to be put on the mailing list.

## Roadmap

1. **Reach out to the community (continuous)**

2. **Request for documentation:** 
   * Goal: document how various accelerator modeling codes already do for their lattices.
   * Organizers: Create a template.
     * element parameters
     * lines/beamlines: how are elements distributed
     * example of a facility/lattice
   * Community: to fill out a template per existing code.

3. **Request for proposals:** how would we like to describe lattices and individual elements in terms of physical line & element properties?
   * Goal: write down as text document (standard)
     * use a simple bullet point document, e.g., with name of element, purpose, properties and alternates
   * Form community consensus on:
     * elements: which properties to put in the standard we are developing; which alternate descriptions do we need for the same element?
     * lines/beamlines: which descriptions (e.g., sub-lines, channels/repetitions, line order inversion, physical line inversion, constants/variables to scale lines, etc.) do we need in the standard we are developing or which ones belong in implementations

4. **Request for proposals:** how would we like to store & exchange lattices in the 21st century?
   * Goal: document advise to implementers.
   * How can we programming language agnostic store and exchange lattices?
     * examples for storage formats: TOML, YAML, JSON, XML, ... (with the right framework, one can support them all)
     * examples for frameworks (import/export/validation): [pydantic](https://docs.pydantic.dev) (Python), [StructTypes.jl](https://github.com/JuliaData/StructTypes.jl)/[Parameters.jl](https://github.com/mauro3/Parameters.jl) in Julia, etc.
