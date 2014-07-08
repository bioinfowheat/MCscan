## Introduction
The ordering and orientation of genomic scaffolds to reconstruct chromosomes is an essential step during *de novo* genome assembly. This process is often assisted by various mapping techniques. Because each map provides a unique line of evidence, a combination of multiple maps can greatly improve the accuracy of the resulting chromosomal assemblies. ALLMAPS is capable of computing a scaffold ordering that maximizes the colinearity to a collection of maps, including genetic, physical or comparative maps into the final chromosome build. We highlight several salient features of ALLMAPS. 

* Clear, computable objective which is to maximize the colinearity to multiple input maps - **reproducible**
* Allow variable weights in input maps, leading to better control in conflict resolution - **flexible**
* Can incorporate [many mapping evidence](wiki/ALLMAPS:-How-to-use-different-types-of-genomic-maps), requiring minimal transformation - **powerful**
* Streamline the genome build (with FASTA and AGP outputs) and genome update (with CHAIN output) - **easy-to-use**

## Installation
### Python dependencies
* [Biopython](http://biopython.org/)
* [Numpy](http://numpy.org)
* [DEAP](https://code.google.com/p/deap/)
* [NetworkX](https://networkx.github.io/)
* [Matplotlib](http://matplotlib.org) (required only for plotting)
* [brewer2mpl](https://github.com/jiffyclub/brewer2mpl) (required only for plotting)

Most of the Python libraries can be installed using `easy_install` or `pip install`, for example:
```
easy_install deap
```

Finally, make sure that you install [jcvi](https://github.com/tanghaibao/jcvi/) repository and put the directory on your `PYTHONPATH`. For example if you want to install it to `~/code`, do the following:
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

These external dependencies are available in my repo [jcvi-bin](https://github.com/tanghaibao/jcvi-bin), which are compiled on Linux (CentOS6). You need to copy the following binaries to a directory that's on you `PATH`.
```
$ git clone git@github.com:tanghaibao/jcvi-bin.git
$ cp jcvi-bin/bin/* /usr/local/bin
```

If they fail to execute, please get concorde from [here] (http://www.math.uwaterloo.ca/tsp/concorde.html), faSize/liftOver from [here](http://hgdownload.cse.ucsc.edu/admin/jksrc.zip) and recompile if necessary. If you are somehow stuck, please check out [our installation guide in heavy details](wiki/ALLMAPS:-How-to-install).

## Walk-through example
We would like to use a small example to showcase what you can do with ALLMAPS. In this example, we have two maps, and scaffold sequences. Our goal is to use the two maps, to order and orient the genomic scaffolds into chromosomes. Download the test dataset [here](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS-testdata/ALLMAPS-testdata.zip). We have the following files:
* scaffold sequences (`scaffolds.fasta`)
* Map 1 (`JMMale.csv`)
* Map 2 (`JMFemale.csv`)

That's it. Now let's watch how the magic unfolds.

##### Step 1. Prepare input data
```
Scaffold ID, scaffold position, LG, genetic position
```
![Genetic map format](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS-testdata/Map-format.png)

You can do this in EXCEL, but remember to save as "comma-separated format".
![Save file in CSV](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS-testdata/CSV-saving.png)

For the test maps, both maps are properly formatted, so you can skip this step. Additionally, please note that ALLMAPS support a wide array of mapping evidence. For possible conversion for your own data, check article on [How to use different types of genomic maps](wiki/ALLMAPS:-How-to-use-different-types-of-genomic-maps).

##### Step 2. Merge the two maps together
```
python -m jcvi.assembly.allmaps merge JMMale.csv JMFemale.csv -o JM-2.bed
```

##### Step 3. Modify the weights file (`weights.txt`) if needed. Default is every map set to weight of 1.
```
JMFemale 1
JMMale 1
```

##### Step 4. Run scaffold ordering, which takes your merged bed, weights file and original scaffold FASTA file.
```
python -m jcvi.assembly.allmaps path JM-2.bed weights.txt scaffolds.fasta
```

##### Step 5. Build release
```
python -m jcvi.assembly.allmaps build JM-2.chr.agp scaffolds.fasta JM-2.bed
```

We now have a release!
* `JM-2.fasta` - reconstructed chromosome sequences
* `JM-2.agp` - order and orientations of the scaffolds, which can be used in Genbank submissions
* `JM-2.chain` - useful to convert scaffold coordinates to new coordinates, for example, if you annotated gene models using the scaffolds, you can use this file along with `liftOver` to transfer the genes onto chromosomes

##### Optional. Plot alignments
```
python -m jcvi.assembly.allmaps plotall JM-2.lifted.bed JM-2.agp weights.txt
```
![chr23 alignments](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS-testdata/chr23.png)

##### Optional. Summary statistics
```
python -m jcvi.assembly.allmaps summary JM-2.chr.agp scaffolds.fasta JM-2.bed
```
This command will create following tables, summarizing various stats before and after the scaffold anchoring.
```
*** Summary for each individual map ***
======================================================================
                         o              JMFemale                JMMale
----------------------------------------------------------------------
            Linkage Groups                     1                     1
                   Markers                    61                    59
             N50 Scaffolds                     5                     5
                 Scaffolds                    18                    18
   Scaffolds with 1 marker                     7                     7
  Scaffolds with 2 markers                     3                     5
  Scaffolds with 3 markers                     1                     0
Scaffolds with >=4 markers                     7                     6
               Total bases    14,691,276 (96.2%)    13,781,640 (90.2%)
----------------------------------------------------------------------
*** Summary for consensus map ***
=============================================================
                         o               Anchored    Unplaced
-------------------------------------------------------------
                   Markers                    120           0
             N50 Scaffolds                      5           0
                 Scaffolds                     22           0
   Scaffolds with 1 marker                      5           0
  Scaffolds with 2 markers                      7           0
  Scaffolds with 3 markers                      1           0
Scaffolds with >=4 markers                      9           0
               Total bases    15,270,543 (100.0%)    0 (0.0%)
-------------------------------------------------------------
```
The anchor rate for the consensus map reaches 100%, which is better than using any single map alone. Hooray!

## Real-world examples
Two studies `medicago` and `yellow-catfish`, described in details in the ALLMAPS manuscript, can be run to get further familiarized with ALLMAPS. See `run.sh` in respective folder on running ALLMAPS on these two data sets. 
* Tang, Haibao (2014): ALLMAPS supporting data: Yellow catfish genome assembly. figshare. 
http://dx.doi.org/10.6084/m9.figshare.1057746
* Tang, Haibao (2014): ALLMAPS supporting data: Medicago genome assembly. figshare. 
http://dx.doi.org/10.6084/m9.figshare.1057745

