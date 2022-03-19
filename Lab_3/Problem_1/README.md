# Problem 1: Iron - Stability Under Pressure and Magnetism 

You will calculate energies of different structures using the Perdew-Burke-Ernzerhof(PBE) GGA exchange-correlation functional. Use a wavefunction cutoff of 30 Ry along with charge density cutoff 300 Ry. A good k-point mesh is not given to you; you will have to find this yourself. We are going to compare the energies of the HCP and BCC structures. Note that the energy difference will be very small. Energies should be converged to within 2meV/atom.

1. Using PWscf, optimize the lattice parameters for both BCC and HCP Fe (i.e., a for BCC and a and c for HCP). Plot the ground state energy of Fe in both structures as a function of k-points. You may choose to do automatic optimization or directly scan a range of lattice parameters.

2. Plot the energy as a function of unit cell volume for the two structures and find when the HCP structure becomes more favorable than the BCC one. (note that: At pressure greater than 15 GPa the HCP-Fe phase is more stable [see “Possible thermal and chemical stabilization of body-centred-cubic iron in the Earth’s core”, Vocadlo L. et al. Nature, 424 (2003) 536; Xianwei Sha and RE Cohen PRB, 73 (06) 104303]). Further, note that it is important when comparing energies that the k-point samplings for both systems are comparable and converged.


3. Using PWscf, calculate and compare the total energy for the BCC structure in the ferromagnetic, antiferro-magnetic, and nonmagnetic states. Think about which unit cells and initial conditions to use. What is the ground state magnetic ordering?
