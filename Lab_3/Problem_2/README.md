# Problem 2: Stability of the perovskite: A case study Lead Titanate 

PbTiO3 is a perovskite oxide which is ferroelectric. The ferroelectric response of PbTiO3 is the result of a displacive transition where a low temperature tetragonal phase is preferred over the cubic phase. In this problem, we will study the energetics of cubic PbTiO3 and use first principles calculations to gather information pertaining to the displacive transition to the tetragonal phase.

1. Calculate and plot the energy of cubic PbTiO3 as a function of lattice parameter. Use a 4 × 4 × 4 k-point mesh with a 1,1,1 offset (see example script in the handout). Sample lattice parameters with a sufficiently fine grid to get a reliable value for the equilib- rium lattice constant. To get an idea where to begin, note that the room-temperature experimental lattice constant is about 3.97 ̊A.

2. Using the equilibrium lattice parameter from part (A), plot the energy as a function of displacement of the Ti atom along one of the cubic lattice directions, allowing the O atoms to fully relax for each displacement. Report the Ti displacement at which the total energy is at a minimum. What is the energy difference between this configuration and the minimum-energy configuration from part (A)? Be aware that for PbTiO3, the Ti displacement will be very small.


3. Now allow both the Ti atom and the O atoms to relax and find the minimum energy structure, using the minimum-energy Ti displacement from part (B) as your starting configuration. Report the final atomic positions and final energy.

4. Which phase is the most energetically stable for PbTiO3 and how does that relate to the ferroelectric behavior of this material?

## Part 1: Energy of Cubic PbTiO3
First, we need to generate the structure of Cubic PbTiO3. This is a slightly more involved structure so to generate this structure we can use ASE, Atomic Simulation Environment. In particular, we can use the following script 
``` python3
from ase import Atom
from ase.io import write 
import numpy as np 

def make_struc(alat, displacement=0):
    """
    Creates the crystal structure using ASE.
    :param alat: Lattice parameter in angstrom
    :return: structure object converted from ase
    """
    
    lattice = alat * np.identity(3)
    symbols = ['Pb', 'Ti', 'O', 'O', 'O']
    sc_pos = [[0,0,0], [0.5,0.5,0.5 + displacement], [0,0.5,0.5], [0.5,0,0.5], [0.5,0.5,0]]
    perov = Atoms(symbols=symbols, scaled_positions=sc_pos, cell=lattice)
    
    # check how your cell looks like
    write('s.cif', perov)
```
This generates a cif file that we can then import into a visualizer like Vesta. In our case, we have 
<p align = 'center'> 
<img width="325" alt="Screen Shot 2022-03-21 at 8 17 33 AM" src="https://user-images.githubusercontent.com/76876169/159292587-1268b95e-6613-4bc6-9186-6c08bc824d94.png">
</p> 

Then we can import this into a quantum espresso  

### 
