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
We try a different function here. We start by explicitly writing an input file by hand, noting that all we need to do is change the `ecutwfc` value. So we write a function that takes a text file, parses through, re-writes the new line, and exits. 
```python3
def ecutwfc_subs(file, cut_off_value):
    """ opens input file file and changes value for ecutwfc """
    
    # prepare new string
    new_string = "    ecutwfc = " + str(cut_off_value) + ",\n"
    
    # open the file 
    with open(file,'r') as input_file:
        lines = input_file.readlines()
    with open(file, 'w') as input_file:
        for line in lines:
            if line.split()[0] == 'ecutwfc':
                input_file.write(new_string)
            else:
                input_file.write(line) 
```
Then, declaring an array of cut off energies 
```python3
cut_off_array = np.arange(5,85,5)
```
We can then encapsulate the main part of the problem in the following script
```python3
def problem_3(cut_off_array):
    """ performs problem 3 calculations """
    
    # declare force array of same size as cut off array 
    force_array = np.zeros(len(cut_off_array))
    
    for i in range(len(cut_off_array)):
        # update input file 
        ecutwfc_subs("Ge.scf.in", cut_off_array[i])
        
        # call pw.x 
        subprocess.run('pw.x -in Ge.scf.in > ge.scf.out', shell=True)
        
        # parse output file 
        result = parse_output('ge.scf.out')
        
        # get force and append to array 
        force_array[i] = result['force']
        
    return force_array
```



## Results 
Plotting the results, we find 
<p align="center">
<img width="739" alt="Screen Shot 2022-03-18 at 7 18 56 PM" src="https://user-images.githubusercontent.com/76876169/159103022-3ba3f0ad-232b-4b79-8799-93f5467e4d9f.png">
</p>
and through some analysis we can find that convergence occurs around 35-40 Rydberg 
```
Cut Off: 5 , Difference: 0.0
Cut Off: 10 , Difference: 1.10741024
Cut Off: 15 , Difference: -1.7823405600000002
Cut Off: 20 , Difference: 0.025806739999999995
Cut Off: 25 , Difference: 0.008259340000000004
Cut Off: 30 , Difference: 0.004346849999999999
Cut Off: 35 , Difference: 0.00039873999999999465
Cut Off: 40 , Difference: 6.0930000000000706e-05
Cut Off: 45 , Difference: -5.1540000000002695e-05
Cut Off: 50 , Difference: 3.11600000000023e-05
Cut Off: 55 , Difference: 9.220000000004225e-06
Cut Off: 60 , Difference: -2.130000000002963e-06
Cut Off: 65 , Difference: 2.264999999999906e-05
Cut Off: 70 , Difference: 5.480000000000762e-05
Cut Off: 75 , Difference: -6.49600000000028e-05
Cut Off: 80 , Difference: 2.4360000000001047e-05
```

