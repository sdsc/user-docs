Trestles User Guide: Benchmarks
===============================

Code & Version  |Benchmark  |Processes  |Threads/Process    |Runtime (m:s)  |Comments
----------------|-----------|-----------|-------------------|---------------|----------
ABySS 1.2.5     |Ecoli.k31  |8          |1                  |8:25           |details
SOAPdenovo 1.05 |Ecoli.k31  |1          |8                  |3:26           |details
Velvet 1.012    |Ecoli.k31  |1          |1                  |8:38           |details
MAFFT 6.843     |BB30003    |1          |32                 |0:17           |details
MrBayes 3.1.2   |RDPII_218  |8          |4                  |1:42           |details
RAxML 7.2.7     |RDPII_218  |10         |6                  |6:16           |details
Amber           |dhfr       |64         |1                  |6:42           |details
NAMD            |ApoA1      |64         |1                  |3:49           |details

[ABySS](http://www.bcgsc.ca/platform/bioinfo/software/abyss)
---------------------------------------------
ABySS is a de novo genome assembler for short DNA reads that is parallelized with MPI. The benchmark uses a k-mer size of 31 to assemble the E. coli genome from the SRX000429 data set of 20.8M Illumina paired-end reads that are 36 bp long. The reported time is for 8 MPI processes. The benchmark script can be found in /home/diag/opt/abyss/1.2.5/examples/script.run.

[SOAPdenovo](http://soap.genomics.org.cn/soapdenovo.html)
---------------------------------------------
SOAPdenovo is a de novo genome assembler for short DNA reads that is parallelized with Pthreads. The benchmark uses a k-mer size of 31 to assemble the E. coli genome from the SRX000429 data set of 20.8M Illumina paired-end reads that are 36 bp long. The reported time is for 8 threads. The benchmark script can be found in /home/diag/opt/soapdenovo/1.05/examples/script.run.

[Velvet](http://www.ebi.ac.uk/~zerbino/velvet/)
---------------------------------------------
Velvet is a de novo genome assembler for short DNA reads that runs serially. The benchmark uses a k-mer size of 31 to assemble the E. coli genome from the SRX000429 data set of 20.8M Illumina paired-end reads that are 36 bp long. Since the code is serial, the reported time is on a single core. The benchmark script can be found in /home/diag/opt/velvet/1.0.12/examples/script.run.

[MAFFT](http://mafft.cbrc.jp/alignment/software/)
---------------------------------------------
MAFFT is a multiple sequence alignment code that is parallelized with Pthreads. The benchmark uses the linsi option to align the BB30003 data set from BAliBASE Version 3. This data set has 142 sequences and 451 sites, including gaps. The reported time is for 32 threads. The benchmark script can be found in /home/diag/opt/mafft/6.843/examples/script.run.

[MrBayes](http://mrbayes.csit.fsu.edu/)
---------------------------------------------
MrBayes is a code for inferring phylogenetic trees from a multiple sequence alignment using a Bayesian approach. The code has a hybrid, multi-grained parallel implementation via MPI and OpenMP. The benchmark consists of the first 10,000 generations in an analysis of the RDPII_218 data set, which has 218 taxa and 2,294 characters. The reported time is for 8 MPI processes and 4 OpenMP threads/process running on a total of 32 cores. The benchmark script can be found in /home/diag/opt/mrbayes/3.1.2h/examples/script.run.

[RAxML](http://icwww.epfl.ch/~stamatak/index-Dateien/Page443.htm)
---------------------------------------------
RAxML is a code for inferring phylogenetic trees from a multiple sequence alignment using a maximum likelihood (ML) approach. The code has a hybrid, multi-grained parallel implementation via MPI and Pthreads. The benchmark consists of 100 bootstraps followed by a full ML search for the RDPII_218 data set, which has 218 taxa and 2,294 characters. The reported time is for 10 MPI processes and 6 threads/process running on a total of 60 cores. The benchmark script can be found in /home/diag/opt/raxml/7.2.7/examples/script.run.

[Amber](http://ambermd.org/)
---------------------------------------------
Amber is a package of programs for molecular dynamics simulations of proteins and nucleic acids. The benchmark is a short dynamics run for the solvated enzyme dhfr (Dihydrofolate reductase), which, among other things, plays a role in the production of DNA. The reported time is for 64 MPI processes running for 5000 dynamic steps. The benchmark script can be found in /home/diag/opt/amber/examples/script.run.

[NAMD](http://www.ks.uiuc.edu/Research/namd/)
---------------------------------------------
NAMD is a parallel molecular dynamics code designed for high-performance simulation of large biomolecular systems. The benchmark is a short dynamics simulation of Apolipoprotien A-1: a protein that consitutes about 70% of the protein in HDL (High Density Lipoproteins). It is produced in the liver and intestines and playes a role in removing bad cholesterol from arterial walls. The reported time is for 64 MPI processes running for 5000 dynamic steps. The benchmark script can be found in /home/diag/opt/namd/examples/script.run.
