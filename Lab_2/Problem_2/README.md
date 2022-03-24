# Problem 2: Convergence of Absolute Energies with Respect to K-Points

A. Using PWscf, calculate the energy as a function of the size of the k-point grid used to integrate the Brillouin zone. For each grid, record the number of unique k-points. Record the computational time. When changing the size of the k-point grid, make sure to keep your other variables fixed (lattice constant, plane-wave cutoff, etc). One may choose a lower cutoff than the “converged” cutoff in the last problem. There are some “cross effects” in doing so, however we assume these are small.

B. Do you see a trend in your calculated energies and computation times with respect to the k-point grid size? If you see a trend, is this what you expect and why?


## Background Information

This problem tests convergence with respect to k-points; so it's similar to problem 1 except we just change the thing that we vary. So we proceed in the same manner. 
- We set up a standard SCF calculation in quantum espresso for a specified structure (and get the absolute energy).
- Scripting to automate the changing/iteration of the cut off energies. We start with a template input file and use Python to update the k-points attribute at each run and then call `pw.x` via command line tools in python. 
- Read out the data to a file for plotting the energy versus number of k-points used. 



## Input File

The core of each PW scf calculation starts with an input file that we can call `ge.scf.in` for our case of a scf calculation for Ge. To run this calculation, we would call `pw.x -in ge.scf.in > ge.scf.out` where `ge.scf.out` is the output file (assuming that `pw.x` is in our path). In particular, the input file that we use will look like 
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

## Python Scripting in Jupyter 

### Parsing Function
We want to parse the output file and extract the energy. We can do this using the following function which goes line by line in the output file to find the correct line, converts the line into an array of strings (using Python split), and selects the correct element in the array to be stored in a dictionary which is returned. 

```python3
def parse_output(outfile):
    """ Parses the quantum espresso output file """
    
    with open(outfile, 'r') as outf:
        for line in outf:
            if (line.lower().startswith('     lattice parameter (alat)')):
                lattice_constant = float(line.split()[-2]) * 0.529177
            if (line.lower().startswith('!    total energy')):
                total_energy = float(line.split()[-2]) * 13.605698066
    
    result = {'energy': total_energy, 'lattice': lattice_constant}
    return result 
```


## Results 
