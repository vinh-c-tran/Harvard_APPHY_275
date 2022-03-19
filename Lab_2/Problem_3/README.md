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

