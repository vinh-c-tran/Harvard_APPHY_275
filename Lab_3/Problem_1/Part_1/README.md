# Problem 1: Part 1 - Lattice Optimization for BCC and HCP Fe
Using the Perdew-Burke-Ernzerhof(PBE) GGA exchange-correlation functional, wavefunction cutoff of 30 Ry along with charge density cutoff 300 Ry.

1. Using PWscf, optimize the lattice parameters for both BCC and HCP Fe (i.e., a for BCC and a and c for HCP). 
2. Plot the ground state energy of Fe in both structures as a function of k-points. You may choose to do automatic optimization or directly scan a range of lattice parameters.

## Background/Setup 
We will need to write an input file to pass to `pw.x` that builds a BCC and HCP structure for `Fe`, then call a relaxation calculation to determine the ideal lattice constants. 
1. To do the former, we can note that under the `SYSTEM` card that `ibrav` indicates the crystal structure. Specifically, setting `ibrav = 3` corresponds to a BCC structure and `ibrav = 4` corresponds to a HCP structure.  
2. For the latter, we can call a `calculation = 'vc-relax'` in the `control` card and extract the converged constants at the end of the output file `fe.scf.out`.

One final thing to take into consideration is the fact that iron is magnetic so we take this spin-polarization into account by adding a `nspin` and `starting_magnetization` variables. This is in addition to the metallic variables of `occupations`, `smearing`, and `degauss`. 

### BCC Fe
We start with the following for BCC Fe. In particular, we choose `ibrav = 3` and call a `calculation = 'vc-relax'` with the following input file. 

```fortran
 &CONTROL
    calculation = 'vc-relax',
    prefix      = 'Fe',
    outdir      = '/tmp/',
    pseudo_dir  = '.',
 /

 &SYSTEM
    ibrav     =  3,
    celldm(1) =  4.8,
    nat       =  1,
    ntyp      =  1,

    ecutwfc =  30.0,
    ecutrho =  240.0,
    
    occupations =  'smearing',
    smearing    =  'methfessel-paxton',
    degauss     =  0.03,

    nspin = 2, 
    starting_magnetization(1) = 0.6,
 /

 &ELECTRONS
    conv_thr = 1d-10,
 /
 
 &IONS
 /
 &CELL
 /

ATOMIC_SPECIES
   Fe 1.0 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
   
ATOMIC_POSITIONS crystal
   Fe       0.000000000   0.000000000   0.000000

K_POINTS automatic
   6   6   4   1   1   1 
! coarse k-mesh as to speed-up
```
After calling this script in the terminal using `pw.x -in <input_file.in> > <output_file.out>` we then want to extract the lattice parameter. In this case, we can do this by checking the positions before and after the relaxation. These are usually given in units of the lattice parameters (alat) and we can compare, using our initial guess to extract it.

Calling `grep "a(1) = (" <output_file>` for instance would return something of the form (for BCC lattice)
```terminal
               a(1) = (   1.000000   0.000000   0.000000 )  
               a(1) = (   0.843806  -0.000000   0.000000 )  
```
which we know has `v(1) = a (1, 0, 0)` so we can use
```math
 0.843806/1.00 = a_relaxed / a_initial
```
to solve for `a_relaxed`. In particular, we find `a_relaxed = 0.843806 * 4.8 = 4.05` Bohr. 

### HCP Fe


