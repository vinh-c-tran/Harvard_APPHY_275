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
<img width="459" alt="Screen Shot 2022-03-24 at 8 43 47 AM" src="https://user-images.githubusercontent.com/76876169/159955144-361af674-1e2b-4572-b3e3-1f4067174513.png">
</p> 
