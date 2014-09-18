## Introduction
The ordering and orientation of genomic scaffolds to reconstruct chromosomes is an essential step during *de novo* genome assembly. This process is often assisted by various mapping techniques. Because each map provides a unique line of evidence, a combination of multiple maps can greatly improve the accuracy of the resulting chromosomal assemblies. ALLMAPS is capable of computing a scaffold ordering that maximizes the colinearity to a collection of maps, including genetic, physical or comparative maps into the final chromosome build. We highlight several salient features of ALLMAPS. 

* Clear, computable objective which is to maximize the colinearity to multiple input maps - **reproducible**
* Allow variable weights in input maps, leading to better control in conflict resolution - **flexible**
* Can incorporate [many mapping evidence](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS:-How-to-use-different-types-of-genomic-maps), requiring minimal transformation - **powerful**
* Streamline the genome build (with FASTA and AGP outputs) and genome update (with CHAIN output) - **easy-to-use**

## Installation
### Python dependencies
* [Biopython](http://biopython.org)
* [Numpy](http://numpy.org)
* [DEAP](https://code.google.com/p/deap)
* [NetworkX](https://networkx.github.io)
* [Matplotlib](http://matplotlib.org)
* [jcvi](https://github.com/tanghaibao/jcvi)

You should be able to install all Python dependencies with the following command (and wait for the installation to complete).
```
easy_install jcvi
```
If you are somehow stuck, you might need to install the libraries manually, please check out [our installation guide in heavy details](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS:-How-to-install).

### External dependencies
* [concorde](http://www.math.uwaterloo.ca/tsp/concorde.html)
* [faSize](http://hgdownload.cse.ucsc.edu/admin/exe/)
* [liftOver](http://hgdownload.cse.ucsc.edu/admin/exe/)

Use the following script to download Linux binaries. You need to copy the binaries to a directory that's on your `PATH` (for example ~/bin).
```
wget https://www.dropbox.com/s/onsjieazu0uytgk/ALLMAPS-install.sh && sh ALLMAPS-install.sh
cp concorde faSize liftOver ~/bin/
```

If you are somehow stuck, please check out [our installation guide in heavy details](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS:-How-to-install).

## Walk-through example
We would like to use a small example to showcase what you can do with ALLMAPS. In this example, we have two maps, and scaffold sequences. Our goal is to use the two maps, to order and orient the genomic scaffolds into chromosomes. Download the test dataset [here](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS/ALLMAPS-testdata.zip). We have the following files:
* scaffold sequences (`scaffolds.fasta`)
* Map 1 (`JMMale.csv`)
* Map 2 (`JMFemale.csv`)

That's it. Now let's watch how the magic unfolds.

#### Step 1. Prepare input data
```
Scaffold ID, scaffold position, LG, genetic position
```
![Genetic map format](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS/Map-format.png)

You can do this in EXCEL, but remember to save as "comma-separated format".
![Save file in CSV](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS/CSV-saving.png)

For the test maps, both maps are properly formatted, so you can skip this step. Additionally, please note that ALLMAPS support a wide array of mapping evidence. For possible conversion for your own data, check out article on [How to use different types of genomic maps](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS:-How-to-use-different-types-of-genomic-maps).

#### Step 2. Merge the two maps together. This will generate a weights file (`weights.txt`) and the input bed file (`JM-2.bed`).
```
python -m jcvi.assembly.allmaps merge JMMale.csv JMFemale.csv -p JM-2
```

#### Step 3. Modify the weights file (`weights.txt`) if needed. Default is every map set to weight of 1.
```
JMFemale 1
JMMale 1
```

#### Step 4. Run scaffold ordering, which takes the input bed file and original scaffold FASTA file.
```
python -m jcvi.assembly.allmaps path JM-2 scaffolds.fasta
```

#### Step 5. Build release
```
python -m jcvi.assembly.allmaps build JM-2 scaffolds.fasta
```

We now have a release!
* `JM-2.fasta` - reconstructed chromosome sequences
* `JM-2.agp` - order and orientations of the scaffolds, which can be used in Genbank submissions
* `JM-2.chain` - useful to convert scaffold coordinates to new coordinates, for example, if you annotated gene models using the scaffolds, you can use this file along with `liftOver` to transfer the genes onto chromosomes


## Advanced topics
#### Plot alignments
```
python -m jcvi.assembly.allmaps plotall JM-2
```
![chr23 alignments](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS/chr23.png)

#### Summary statistics
```
python -m jcvi.assembly.allmaps summary JM-2 scaffolds.fasta
```
This command will create following tables, summarizing various stats before and after the scaffold anchoring.
```
*** Summary for each individual map ***
======================================================================
                         o              JMFemale                JMMale
----------------------------------------------------------------------
            Linkage Groups                     1                     1
          Markers (unique)                    61                    59
            Markers per Mb                   4.2                   4.3
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
          Markers (unique)                    120           0
            Markers per Mb                    7.9           0
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

#### Estimate gap lengths
ALLMAPS can estimate sizes for inter-scaffold gaps, through the conversion from genetic distance (for example, centi-Morgan used in genetic maps) to physical distance. The default inter-scaffold gap size is 100, which according to Genbank convention, indicates a gap with unknown size. The following command will re-estimate gap sizes based on distance conversion, and generate a new AGP file `JM-2.estimategaps.agp`.
```
python -m jcvi.assembly.allmaps estimategaps JM-2
```

For more details, please check out [ALLMAPS: How to estimate gap sizes](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS:-How-to-estimate-gap-sizes)

#### Use different types of genomic maps in ALLMAPS
The above example assumes genetic maps as input data. For conversion from other types of input data, please check out [How to use different types of genomic maps](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS:-How-to-use-different-types-of-genomic-maps).

## Real-world examples
Two studies `medicago` and `yellow-catfish`, described in details in the ALLMAPS manuscript, can be run to get further familiarized with ALLMAPS. See `run.sh` in respective folder on running ALLMAPS on these two data sets. 
* Tang, Haibao (2014): ALLMAPS supporting data: Yellow catfish genome assembly. figshare. 
http://dx.doi.org/10.6084/m9.figshare.1057746
* Tang, Haibao (2014): ALLMAPS supporting data: Medicago genome assembly. figshare. 
http://dx.doi.org/10.6084/m9.figshare.1057745