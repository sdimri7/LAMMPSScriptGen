# Understanding the Calculation: Equilibrium Lattice Constant and Cohesive Energy

This simple LAMMPS script is designed to find two fundamental properties of a crystalline solid (here, a generic single‐element metal) through **static energy minimization**:

1. **Equilibrium Lattice Constant**  
2. **Cohesive Energy**

***

## 1. Equilibrium Lattice Constant

- A **lattice constant** is the repeating distance between atoms in a crystal. For a face‐centered cubic (fcc) lattice, it is the edge length `a` of the cubic unit cell.  
- The **equilibrium** value of this constant is the one at which the total potential energy of the infinite crystal is **lowest** under zero external pressure.  
- In practice, we:
  1. Start with an **initial guess** for the lattice constant (e.g., 4 Å).  
  2. Allow the simulation box to **relax** (via `fix box/relax`) so the box size changes to minimize energy.  
  3. After minimization, the final box length `lx` (variable `${length}` in the script) corresponds to the **equilibrium lattice constant**.

***

## 2. Cohesive Energy

- **Cohesive energy** is the energy required to **separate** a crystal into **individual, isolated atoms** at rest, per atom. It measures the **strength of bonding** in the solid.  
- Formally:  
  $$ E_\text{coh} = \frac{E_\text{total} - N \, E_\text{atom}}{N} $$  
  where:
  - $$E_\text{total}$$ is the minimized potential energy of the $$N$$-atom crystal.  
  - $$E_\text{atom}$$ is the energy of a single, isolated atom (often defined as zero in many potentials).  
- In our script:
  - `compute eng all pe/atom` gathers each atom’s potential energy.  
  - `compute eatoms all reduce sum c_eng` sums these, giving $$E_\text{total}$$.  
  - Since an isolated atom has zero energy in most EAM potentials,  
    $$ E_\text{coh} = \frac{E_\text{total}}{N} = \frac{\text{c\_eatoms}}{\text{natoms}}. $$  
  - The script calculates and prints this as `${ecoh}`.

***

## How the Script Achieves These Calculations

1. **Setup**  
   - Defines an fcc lattice of atoms with initial lattice constant `${lattice_constant}`.  
   - Builds a simulation box and populates it with atoms.

2. **Potential and Relaxation**  
   - Uses an EAM potential (`pair_style eam/alloy`) appropriate for the element.  
   - Applies `fix box/relax iso 0.0` to let the box size change under zero pressure.

3. **Minimization**  
   - Runs `minimize` to adjust atomic positions and box dimensions to find the **lowest energy configuration**.

4. **Extraction**  
   - Reads final box length (`lx`) as the **equilibrium lattice constant**.  
   - Computes total potential energy per atom as the **cohesive energy**.

5. **Output**  
   - Prints:  
     - **Total energy** (`${teng}`)  
     - **Number of atoms** (`${natoms}`)  
     - **Lattice constant** (`${length}`)  
     - **Cohesive energy** (`${ecoh}`)  

***

By running this script, you automatically determine how tightly atoms pack (equilibrium lattice constant) and how strongly they bind together (cohesive energy) for any single‐element metal given the appropriate potential file.