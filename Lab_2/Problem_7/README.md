# Problem 7: Equilibrium Lattice Constant and Bulk Modulus

In this problem you will calculate the equilibrium lattice constant and bulk modulus of Ge. Usually, we are interested in quantities such as forces or energy differences, not absolute energies. Therefore, for this problem use the cutoff and k-point criteria that you determined for the force and energy difference calculation. (In general, to be absolutely safe you should test convergence specifically for the quantity you are interested in. So, ideally, we would test convergence of lattice constant as a function of energy cutoff and k-point grid size, but we are not going to do this.)

1. Calculate the equilibrium lattice constant of Ge in the diamond structure from first principles using PWscf. The experimental value is 10.7 bohr. How does the experimental value compare with the calculated value? Is this expected? Make sure to record all the relevant parameters (k-points, cutoffs, etc).

2. Calculate the bulk modulus of Ge in the diamond structure. Here you will need to derive some (simple) equations and then apply them to compute a material’s property, a very typical scenario in computational science. The bulk modulus is a measure of the stiffness of a material. It is defined as `B = - V_0 dP/dV` where P is the pressure on the material, V is its volume, and V0 is its equilibrium volume. Derive an expression for the bulk modulus that you can use it. Use finite difference approximation for derivatives. How does your value compare with the experimental value of ≈76 GPa?

3. Solve problem 7A by directly minimizing the energy using structure optimization ca- pability of Quantum Espresso. Think about what type of calculation the code should do, find the required parameters in the online documentation and modify the Python workflow accordingly. Compare with the results of problem 7A where you directly scanned over lattice parameter values.

Hints: Remember that P = −∂E/∂V . Remember that PWscf calculates energies per primitive unit cell. Be careful about unit conversions.

## Part 1
There are two ways to find the equilibrium lattice constant: manually or via a PW-scf function call. 
- Manually: We can run scf calculations while varying the lattice constant `a`. Then we would get `E = E(a)`. Then to this data we can fit it to either a second order Taylor expansion or to an equation of state model which would predict a value for the equilibrium lattice constant. 
- "Automatically": We can run a calculation with `calculation = vc-relax`. This option allows for the atoms in the structure to move and optimizes the positions and hence the lattice constant. 

In part III we will do the automatic option to become more familiar with other methods in quantum espresso, but for part I, we will do this manually. 

Starting with the following generic self-consistent field input file
```fortran
&control
    calculation = 'scf' 
    prefix = 'Germanium',
    pseudo_dir = '.',
    outdir = '.',
/
&system
    ibrav = 2,
    celldm(1) = 10.690181,
    nat = 2,
    ntyp = 1,
    ecutwfc = 20.0,
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
we can then generate a lattice array in Python
```python3
    lattice_array = np.linspace(9.7, 11.7, 10)
```
and use the following script to update the input file with a new lattice constant at each step 
```python3
def lattice_subs(file, lattice_constant):
    """ opens input file file and changes value for celldm(1) = lattice constant """
    
    # prepare new string
    new_string = "    celldm(1) = " + str(lattice_constant) + ",\n"
    
    # open the file 
    with open(file,'r') as input_file:
        lines = input_file.readlines()
    with open(file, 'w') as input_file:
        for line in lines:
            if line.split()[0] == 'celldm(1)':
                input_file.write(new_string)
            else:
                input_file.write(line) 
```
We can then put everything together including the command line call `pw.x -in ge.scf.in > ge.scf.out` using `subprocess` into a single function call
```python3 
def energy_vs_lattice(lattice_array):
    """ performs problem 7 part 1 calculations """
    
    # declare energy array of same size as cut off array 
    energy_array = np.zeros(len(lattice_array))
    
    for i in range(len(lattice_array)):
        # update input file 
        lattice_subs("Ge.scf.in", lattice_array[i])
        
        # call pw.x 
        subprocess.run('pw.x -in Ge.scf.in > ge.scf.out', shell=True)
        
        # parse output file 
        result = parse_output('ge.scf.out')
        
        # get force and append to array 
        energy_array[i] = result['energy']
        
    return energy_array
```
### Part 1: Results 
We perform a fit to the Birch-Murnaghan equation of state [1] using `scipy.optimize.curve_fit` which gives us `a0 = 10.902` which is rather close to the experimental value of `a0 = 10.7`. 
<p align="center">
<img width="708" alt="Screen Shot 2022-03-19 at 5 21 25 AM" src="https://user-images.githubusercontent.com/76876169/159120884-5b3c3ffa-6039-4d88-8273-8ab084289e2e.png">
</p>


## Part 2
We start by noting that  
```math
    dP/dV = - (d/dV)dE/dV = - dE^2/da^2 * da^2/dV^2
```
using the chain rule to re-write these derivatives in terms of a more easily controlled parameter, ie, the lattice constant. 

Then we can calculate the derivative using finite differences. In particular, we can use a five point formula. 
```math
    P''(a) = 1/(12h^2)(-P(x-2h) + 16P(x-h) - 30P(x) + 16P(x+h) - P(x+2h))
```
To this end then, the structure of code is as follows
1. We know from part 1 the predicted equilibrium lattice constant `a0`. Then all we need to do is to run scf calculations, updating the lattice constant to `a0 -2h, a0-h, a0+h, a0+2h`, record the energy values `E=E(a)` and volume values `V=V(a)`. 
2. Then we can calculate the two second derivatives and then take their product to hopefully obtain our result. 

We can use the same code as in Part I, modifying the python parsing function to also extract the unit cell volume
```python3
def parse_output(outfile):
    """ Parses the quantum espresso output file """
    
    with open(outfile, 'r') as outf:
        for line in outf:
            if (line.lower().startswith('     lattice parameter (alat)')):
                lattice_constant = float(line.split()[-2])
            if (line.lower().startswith('!    total energy')):
                total_energy = float(line.split()[-2]) * 13.605698066
            if (line.lower().startswith('     unit-cell volume')):
                volume = float(line.split()[-2])
    
    result = {'energy': total_energy, 'lattice': lattice_constant, 'volume': volume}
    return result 
```
We define the following array for the lattice points
```python
h = 0.01
a0 = 10.902
lattice_array = np.array([a0-2*h, a0-h, a0, a0+h, a0+2*h])
```
and loop through this array, perform SCF calculations, and writing out the data to arrays. 


# References
Sholl, David. "Density Functional Theory. A Practical Introduction". Pg. [38]
