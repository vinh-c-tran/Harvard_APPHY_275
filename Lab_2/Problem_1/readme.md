# Problem 1: Convergence of Absolute Energies with Respect to Cutoff Energies

A. Using Quantum Espresso PWscf, calculate the energy of Ge in the diamond structure as a function of palne-wave cutoff energy. A good range to try is 5-80 Ry, doing calculations at increments of 5 Ry, keeping other variables constant. Record and plot final results. Specify when you reach the level of convewrgence of aorund 5 meV/atom. Note that the code calculates energy per primitive cell (not atom). 

B. Do you see a trend in your calculated energies and calculation time with respect to the cutoff? Is this what you expect and why? 

C. Cubic unit cell vs primitive cell. Advantages and disadvantages of both?

## Background Information

This problem allows for the use of two main ideas
- Setting up a standard SCF calculation in quantum espresso for a specified structure (and get the absolute energy).
- Scripting to automate the changing/iteration of the cut off energies. This we can do in python to generate the input files and call `pw.x` via command line tools in python. 

When we perform a SCF calculation, we have many parameters that we can tune, one of which being the plane-wave cut off as we truncate our sum over plane waves for numerical calculations. As we increase the energy cut off, we'd expect our results to converge closer to the converged value. 


## Input File

The core of each PW scf calculation starts with an input file that we can call `ge.scf.in` for our case of a scf calculation for Ge. In particular, the one that we use will look like 
```
&control
```
