<p align="center">
  <img width="250" height="150" src="logo.png">
</p>

### PROT-ON: A Structure- and Statistics-Based Detection of Designer PROTein Interface MutatiONs

This repo contains the collection of codes to find designer interfacial mutations by setting multiple runs with [EvoEF1](https://github.com/tommyhuangthu/EvoEF). 

### Motivation
  Protein interactions are essential to any biological process. Therefore, understanding the impact of interfacial mutations on protein-protein interactions is vital. In this work, we present our PROT-ON tool, which uses [EvoEF1](https://github.com/tommyhuangthu/EvoEF) or [FoldX](http://foldxsuite.crg.eu/) to scan the impact of all possible interfacial mutations. Our tool  performs a statistical analyis on the scanned mutational landscape to present the mostly-enriching and depleting-mutations. All these analyses take a couple minutes on a standard laptop.
### PROT-ON Architecture
<p align="center">
<img align="center" src="proton_code_architecture.jpg" alt="proton_code_architecture" width = "600" />
</p>

### PROT-ON Input
PROT-ON works on dimers. It takes the coordinate file of a dimer (in pdb format) as an input together with the chain ID that should be modified/scanned by the program. 

If the user would like to incorporate a PSSM-based filter on the predictions, an externally generated PSSM score file (in csv format with the `<root-pdb-filename>_chain_<chain_ID>_pssm.csv` naming) should be placed in the run directory. The PSSM scores (you can obtain from https://possum.erc.monash.edu/server.jsp) should be seperated with a comma `,`. An exemplary PSSM file is located in the `example-run` directory. 

### PROT-ON Output Files
`proton.py` script with the described [Usage](https://github.com/CSB-KaracaLab/prot-on/tree/main#usage) generates: 
  * **Interface amino acid list:** Interfacial amino acid list (within a defined cut-off), belonging to the input chain ID, calculated by `interface_residues.py`. The same script outputs the pairwise contacts, as **Contact list:**
  * **Mutation list:** The list of all possible interfacial mutations (format: KD28A; K: Wild-type amino acid, D: Chain ID, 28: Amino acid position, A: Mutant amino acid)
  * **Mutation models:** Generated mutant models modelled by `BuildMutant` command of EvoEF1 or `BuildModel` command of FoldX.
  * **Individual EvoEF1/FoldX files:** EvoEF1/FoldX binding affinity predictions calculated by `ComputeBinding` of EvoEF1 or `AnalyseComplex` of FoldX.
  * **Boxplot of EvoEF1/FoldX scores:** All EvoEF1/FoldX binding affinity predictions are analyzed with the box-whisker statistics, where;
  * **Depleting mutations:** are defined by the positive outliers, and;
  * **Enriching mutations:** are defined by the negative outliers. 
  * **Heatmap of PROT-ON scores:** All the possible mutation energies are also plotted as a heatmap for visual inspection.
  * **Filtered mutations:** PSSM-filtered (Enriching mutations: PSSM-score >=0 && Depleting mutations: PSSM-score <=0) & stability-probed (uses `ComputeStability` command of EvoEF1 or `Stability` command of FoldX, where DDG-stability<0) enriching and depleting mutations.
### System dependencies
* [FoldX](http://foldxsuite.crg.eu/)
* conda (OR python3)

### Python dependencies
* numpy
* pandas (**should be version 1.3.0 or higher**)
* plotly
* shutil
* time
* kaleido
## Usage
### Clone the repository
```
git clone https://github.com/CSB-KaracaLab/prot-on.git
```
```
cd prot-on
```
After this, the pre-installed EvoEF folder, foldx executable and rotabase.txt files should be moved into the `prot-on` directory and `setup.py` should be run. according to the [EvoEF1](https://github.com/tommyhuangthu/EvoEF) instruction.
### Installation
Run the following to generate the executables for running PROT-ON (which can only run on Linux or MacOS).
```
conda activate
```
```
python proton.py -pdb=<filename of structure> --chain_ID=<chain ID of interest> --cut_off=<cut off to define interface> --IQR=<IQR rule to define outliers of box-and-whisker plot>

Example:

python proton.py --pdb=complex.pdb --chain_ID=D --cut_off=5.0 --IQR=1.5> proton.log
```
If you call python3 independently (not with conda), then you should execute:
``` 
python3 proton.py -pdb=<filename of structure> --chain_ID=<chain ID of interest> --cut_off=<cut off to define interface> --IQR=<IQR rule to define outliers of box-and-whisker plot
```
### Usage of individual scripts
If you want, you can also run the PROT-ON scripts located under `src/` independently. 

As an example, if you are interested only getting the interface information on the complex you study, you can use `interface_residues.py` as in:
```
python inteface_residues.py <filename of structure> <chain ID of interest> <cut off to define interface>

Example:

python inteface_residues.py complex.pdb D 5.0
```
Or if you are insterested just in the binding affinity prediction for a specific mutation list, you can use `energy_calculation_EvoEF.py` as in:
```
python energy_calculation_EvoEF.py <filename of structure> <filename of mutation list> <selected algorithm; EvoEF1: 1, FoldX: 2> 

Example for EvoEF1:

python energy_calculation_FoldX.py complex.pdb D complex_chain_D_mutation_list 1
```
```
Example for FoldX

python energy_calculation_FoldX.py complex.pdb D complex_chain_D_mutation_list 2
```
You can generate boxplot, heatmap, or proposing depleting&enriching mutation list with `detect_outliers.py`:
```
python detect_outliers.py <pdb-filename> <filename of structure> <filename of proton scores> <selected algorithm; EvoEF1: 1, FoldX: 2> <IQR rule to define outliers of box-and-whisker plot>

Example for EvoEF1:

python detect_outliers.py complex.pdb D complex_chain_D_proton_scores 1 1.5

Example for FoldX:

python detect_outliers.py complex.pdb D complex_chain_D_proton_scores 2 1.5
```
## Web Server
You can access user friendly web server of PROT-ON in here: http://proton.tools.ibg.edu.tr:8001
## Acknowledgement
We would like to thank Ayşe Berçin Barlas for her assistance in revising the code architecture. We also thank Eda Şamiloğlu and Mehmet Ergüven for their contribution to the intial phase of the project. Also big thanks to İrem Yılmazbilek for analyzing the case studies to test the PROT-ON performance to detect the crical positions and mutations. 
## Bug Report
If you encounter any problem, you can contact Mehdi or Ezgi via:
## Contacts
* ezgi.karaca@ibg.edu.tr
* mehdi.kosaca@ibg.edu.tr
