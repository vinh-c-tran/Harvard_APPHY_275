# Extra Credit Question 2: Band Structure of Ge

For this problem, we just want to calculate the band structure of Germanium. 

## Band Structure Quantum Espresso 
A band structure calculation consists of three main parts
1. A `pw.x` self-consistent field calculation `calculation = 'scf'`. 
2. A `pw.x` non-self consistent field band calculation `calculation = 'nscf'`
3. A `bands.x` calculation 

This results in the band structure being stored in an output file that we can then plot. 


## Results 
<p align = center> 
[germanium_bands.pdf](https://github.com/vinh-c-tran/Harvard_APPHY_275/files/8343108/germanium_bands.pdf)
</p> 
