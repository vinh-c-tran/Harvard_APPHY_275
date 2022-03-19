# Problem 1: Part 1
Using the Perdew-Burke-Ernzerhof(PBE) GGA exchange-correlation functional, wavefunction cutoff of 30 Ry along with charge density cutoff 300 Ry.

Using PWscf, optimize the lattice parameters for both BCC and HCP Fe (i.e., a for BCC and a and c for HCP). Plot the ground state energy of Fe in both structures as a function of k-points. You may choose to do automatic optimization or directly scan a range of lattice parameters.

## Background/Setup 
We will need to write an input file to pass to `pw.x` that builds a BCC and HCP structure for `Fe`, then call a relaxation calculation to determine the ideal lattice constants. 
1. To do the former, we can note that under the `SYSTEM` card that `ibrav` indicates the crystal structure. Specifically, setting `ibrav = 3` corresponds to a BCC structure and `ibrav = 4` corresponds to a HCP structure.  
2. For the latter, we can call a `calculation = 'sc-relax'` in the `control` card and extract the converged constants at the end of the output file `fe.scf.out`. 

