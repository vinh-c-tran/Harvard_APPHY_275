# Problem 3: Convergence of Forces wrt to plane-wave cutoff energies

Oftentimes we are interested in quantities other than energies. In this problem, we will be calculating forces on atoms. 

Displace a Ge atom +0.05 in the z direction (fractional coordinates). Keeping other parameters fixed, calculate the forces on a Ge atom as a function of the kinetic energy cutoff. A good force value would be converged to within around 10 meV/Angstrom (convert this to Ry/bohr, since PWscf gives forces in Ry/bohr). 

Record relevant parameters (lattice parameter, k-points, unique k-points, etc). A good k-point grid to use is 4 × 4 × 4. Plot and record your results.

## Background 
For this problem we need to do two things: create a new structure with a displaced atom and calculate forces (while varying the cut off energies). 
1. For the former, we just need to update the atomic position of a GE atom in the `ATOMIC_POSITIONS` card. We can take `0.00 0.00 0.00` to then be
2. For the latter, we need to update the input file `ge.prob3.in` to include a `tprnfor = .true.` statement in the `CONTROL` card. 

So for instance, our input file could look like 
```fortran
&control
    prefix = 'Germanium',
    pseudo_dir = '.',
    outdir = './temp',
    tprnfor = .true.,
/
&system
    ibrav = 2,
    celldm(1) = 10.690181,
    nat = 2,
    ntyp = 1,
    ecutwfc = 80.0,
/
&electrons
/
ATOMIC_SPECIES
 Ge 72.64 ge_pbe_v1.4.uspp.F.UPF
ATOMIC_POSITIONS
 Ge 0.00 0.00 0.00
 Ge 0.25 0.25 0.25
K_POINTS automatic
 8 8 8 0 0 0

```
## Initial Results 
We can test to see if our input file works correctly (before iterating over other parameters) and we get the following results in our output file. 
```
Forces acting on atoms (cartesian axes, Ry/au):

     atom    1 type  1   force =     0.00000000    0.00000000   -0.11881234
     atom    2 type  1   force =     0.00000000    0.00000000    0.11881234

     Total force =     0.168026     Total SCF correction =     0.002313
```
We find that the force acting on one atom is `0.11881234` Ry/Bohr. There is also a `Total force = 0.168026` which corresponds to the square root of the sum of the square of the forces acting on each individual atom, ie, `Total force` here is equal to `Sqrt[(-0.11881234)^2 + (0.11881234)^2]`. For the purposes of this problem, we're just interested in the force on a single atom, ie, the former. 

## Python Scripting 
We can largely use the same input file generating functions, modifying the parsing function so that it searched for the force 


## Results 

