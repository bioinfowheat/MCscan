## Introduction

## Installation
### Python dependencies
* [Biopython](http://biopython.org/)
* [Numpy](numpy.org)
* [DEAP](https://code.google.com/p/deap/)
* [NetworkX](https://networkx.github.io/)
* [Matplotlib](matplotlib.org) (required only for plotting)
* [brewer2mpl](https://github.com/jiffyclub/brewer2mpl) (required only for plotting)

Most of the Python libraries can be installed using `easy_install` or `pip install`, for example:
```
easy_install deap
pip install deap
```

Finally, make sure that you install the `jcvi` repository and put the directory on your `PYTHONPATH`. For example if you want to install it to `~/code`, do the following:
```
$ cd ~/code
$ git clone git@github.com:tanghaibao/jcvi.git
$ export PYTHONPATH=~/code:$PYTHONPATH
```
To avoid setting `PYTHONPATH` everytime, please insert the last command in your `.bashrc` or `.bash_profile`.

### External dependencies
* concorde
* faSize
* liftOver

These external dependencies are available in my repo [jcvi-bin](https://github.com/tanghaibao/jcvi-bin), which are compiled on Linux (CentOS6). If they fail to execute, please get concorde from [here] (http://www.math.uwaterloo.ca/tsp/concorde.html), faSize/liftOver from [here](http://hgdownload.cse.ucsc.edu/admin/jksrc.zip) and recompile if necessary. 

## Walk-through example
We would like to use a small example to showcase what you can do with ALLMAPS. In this example, we have two maps, and scaffold sequences. Our goal is to use the two maps, to order and orient the genomic scaffolds into chromosomes.

##### Step 0. What you have so far

##### Step 1. Convert input data to four column csv files:
```
Scaffold ID, scaffold position, LG, genetic positions
```

You can do this in EXCEL, but remember to save as "comma-separated format".

##### Step 2. Merge all three maps together
```
python -m jcvi.assembly.allmaps merge JMMale.csv JMFemale.csv JMF2.csv -o JM-3.bed
```

##### Step 3. Modify the weights file if you want. Default is every map set to weight of 1.
```
JMFemale 1
JMMale 1
JMF2 1
```

##### Step 4. Run scaffold ordering. Which takes your merged bed (step 3), and weights file (step 2) and original scaffold FASTA file.
```
python -m jcvi.assembly.allmaps path JM-3.bed weights.txt genome.fasta
```

##### Step 5. Build release
```
python -m jcvi.assembly.allmaps build JM-3.chr.agp genome.fasta JM-3.bed
```

##### Step 6. Plot
```
python -m jcvi.assembly.allmaps plotall JM-3.lifted.bed JM-3.agp weights.txt
```


