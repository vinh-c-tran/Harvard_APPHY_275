# Problem 7: Equilibrium Lattice Constant and Bulk Modulus

In this problem you will calculate the equilibrium lattice constant and bulk modulus of Ge. Usually, we are interested in quantities such as forces or energy differences, not absolute energies. Therefore, for this problem use the cutoff and k-point criteria that you determined for the force and energy difference calculation. (In general, to be absolutely safe you should test convergence specifically for the quantity you are interested in. So, ideally, we would test convergence of lattice constant as a function of energy cutoff and k-point grid size, but we are not going to do this.)

1. Calculate the equilibrium lattice constant of Ge in the diamond structure from first principles using PWscf. The experimental value is 10.7 bohr. How does the experimental value compare with the calculated value? Is this expected? Make sure to record all the relevant parameters (k-points, cutoffs, etc).

2. Calculate the bulk modulus of Ge in the diamond structure. Here you will need to derive some (simple) equations and then apply them to compute a material’s property, a very typical scenario in computational science. The bulk modulus is a measure of the stiffness of a material. It is defined as `B = - V_0 dP/dV` where P is the pressure on the material, V is its volume, and V0 is its equilibrium volume. Derive an expression for the bulk modulus that you can use it. Use finite difference approximation for derivatives. How does your value compare with the experimental value of ≈76 GPa?

3. Solve problem 7A by directly minimizing the energy using structure optimization ca- pability of Quantum Espresso. Think about what type of calculation the code should do, find the required parameters in the online documentation and modify the Python workflow accordingly. Compare with the results of problem 7A where you directly scanned over lattice parameter values.

Hints: Remember that P = −∂E/∂V . Remember that PWscf calculates energies per primitive unit cell. Be careful about unit conversions.

## Background 

