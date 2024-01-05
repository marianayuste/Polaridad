# Ab-ad Polarity
Codes used in the analysis of the GNR behind aB-aD polarity in _Arabidopsis thaliana_ leaf primordium

## Documents:

#### - radial_exploration.grf
Code used for the radial search in Griffin allowing up to 4 hypothetical interactions (radius 4). Explanation of the input file requirements for Griffin as well as Griffin instalation can be found at [http://turing.iimas.unam.mx/griffin/index.html](https://turing.iimas.unam.mx/griffin/guide.html)

#### - mutant simulations:
Python functions defined to simulate mutants in the boolean networks obtained by Griffin.
The file synchronous_mutants contains the functions that generate mutants using a Synchronous update, the file asynchronous_mutants the functions that use an Asynchronous one.
