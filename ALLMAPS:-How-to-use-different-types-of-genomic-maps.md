## Introduction
The walk-through example was given in anchoring based on genetic maps. ALLMAPS has the ability to go beyond genetic maps to use other types of genomic maps. A combination of several independent lines of evidence will further complement each other in different regions of the genome. One obstacle is that other genomic maps may require some data transformation before being imported into ALLMAPS. 

We note that a generalized form of genomic maps is constituted by markers represented as (x, y) – each marker having a coordinate on the genomic scaffolds (x) and another coordinate on the map (y), respectively. As long as the map can be convert into a list of abstract ‘markers’ carrying these two coordinates, they can be easily integrated in a unified framework.

![](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS/Integrated-maps.png)

## Walk-through example
Different maps have their idiosyncratic formats. For ALLMAPS to process these maps, they have to be converted to  `.bed` format. The conversion can be achieved by your own script, or using our converters. We give a few examples below, but please understand that we cannot accommodate every possible format out there.

We have included using Medicago data as example, [click here to download](https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS/maps.zip).

### Optical map
A popular format for optical mapping is the MapSolver XML format, which looks like (e.g. `omdata/1.xml`):
```
<?xml version="1.0" encoding="iso-8859-1"?>
<aligned_maps_document>

<version>1.0</version>

<experiment>
  <creator>/home/htang/htang/omdata/bin/comp_genomics_first.pl</creator>
  <start_time>1333512196</start_time>
  <soma_version>0.8</soma_version>
  <uuid>00000000-0000-0000-0000-000000000000</uuid>
```
Typically, each chromosome will have a separate `.xml` file. So our commands to convert all 8 chromosomes are:
```
for i in `seq 1 8`; do python -m jcvi.assembly.opticalmap bed --switch --point --scale=100000 omdata/$i.xml; done
cat omdata/?.bed > OpticalMap.bed
```
The converted `OpticalMap.bed` looks like:
```
Scaffold0001    67504   69163   OM_Chr1:294.56259       765.7   +
Scaffold0001    69163   83408   OM_Chr1:294.58039       765.7   +
Scaffold0001    83408   85554   OM_Chr1:294.71839       765.7   +
Scaffold0001    85554   91479   OM_Chr1:294.74179       765.7   +
Scaffold0001    91479   98490   OM_Chr1:294.74179       765.7   +
Scaffold0001    98490   104746  OM_Chr1:294.86479       765.7   +
Scaffold0001    104746  108412  OM_Chr1:294.92359       765.7   +
Scaffold0001    108412  118102  OM_Chr1:294.96119       765.7   +
Scaffold0001    118102  145341  OM_Chr1:295.05409       765.7   +
Scaffold0001    145341  163134  OM_Chr1:295.31489       765.7   +
```

### Genetic Map
We only show case conversion from [MSTMap](http://alumni.cs.ucr.edu/~yonghui/mstmap.html). The MSTmap output `GeneticMap.out` looks like:
```
;number of linkage groups: 12
;The size of the linkage groups are:
;               420     474     376     160     268     173     189     12      1       50      1       1
;The number of bins in each linkage group:
;               59      86      50      33      47      54      36      6       1       12      1       1


;lowerbound:8.716 upperbound: 9.183 cost after initialization:9.254
group lg0
;BEGINOFGROUP
```
Our command for conversion is:
```
python -m jcvi.assembly.geneticmap bed GeneticMap.out --switch
```
The converted `GeneticMap.bed` looks like:
```
Scaffold0006    3409259 3409260 lg0:0.0
Scaffold0006    3409280 3409281 lg0:0.0
Scaffold0006    2298176 2298177 lg0:2.289
Scaffold0006    3210402 3210403 lg0:2.289
Scaffold0006    3210441 3210442 lg0:2.289
Scaffold0006    4907582 4907583 lg0:2.289
Scaffold0006    6286872 6286873 lg0:5.376
Scaffold0006    8883770 8883771 lg0:5.376
Scaffold0006    8883794 8883795 lg0:5.376
Scaffold0006    8883810 8883811 lg0:5.376
```

### Cross-species synteny
Here we showcase conversion from synteny to chickpea. We have the gene pairs (syntelogs) between Medicago and chickpea (`chickpea.medicago.1x1.anchors`), which looks like:
```
###
Ca_02944        Medtr2g105830   2116
Ca_02943        Medtr2g105680   3858
Ca_02942        Medtr2g105640   1908
Ca_02941        Medtr2g105570   1700
Ca_02940        Medtr2g105560   1298
Ca_02939        Medtr2g105480   1228
Ca_02937        Medtr2g105410   1416
Ca_02936        Medtr2g105390   1236
Ca_02935        Medtr2g105360   1414
```
The third column (BLAST score) is NOT really used in ALLMAPS, so you can just put a fake score there (like 100). You can also parse the BLAST results to retrieve this. However, you should consider enriching the syntenic anchors for best results, for example, only using reciprocal best BLAST hit. 

Gene positions for Medicago (`medicago.bed`) and chickpea (`chickpea.bed`):
```
Scaffold0001    322     902     Medtr1g057550   1000    -
Scaffold0001    8954    15489   Medtr1g057560   1000    -
Scaffold0001    16888   18329   Medtr1g057570   1000    +
Scaffold0001    24615   24825   Medtr1g057590   1000    -
Scaffold0001    27702   28374   Medtr1g057600   1000    -
```
```
C11095950       137     470     Ca_28062        0.999968        +
C11097432       315     1878    Ca_28125        0.995213        +
C11100918       992     1250    Ca_28103        0.940937        -
C11101476       54      2195    Ca_28110        0.999684        +
C11101522       84      1376    Ca_28260        0.926188        -
```
Our command for conversion is:
```
python -m jcvi.assembly.syntenypath bed chickpea.medicago.1x1.anchors --switch --scale=100000 -o Chickpea.bed
```
The converted `Chickpea.bed` looks like:
```
Scaffold0001    8954    15489   4:237.28811
Scaffold0001    27702   28374   4:236.88576
Scaffold0001    64515   67197   4:236.69478
Scaffold0001    81629   86232   4:236.34549
Scaffold0001    142080  145143  4:235.54975
Scaffold0001    183586  195244  4:240.49911
Scaffold0001    220621  222448  4:240.83432
Scaffold0001    255488  256172  4:241.1265
Scaffold0001    293880  296258  4:241.83683
Scaffold0001    308173  308518  4:242.08104
```

### Combine everything!
Finally let's merge these evidence together:
```
python -m jcvi.assembly.allmaps mergebed OpticalMap.bed GeneticMap.bed Chickpea.bed -o Mt.bed
```
The merged `Mt.bed` file will have three tiers of evidence:
```
Scaffold0001    8954    15489   Chickpea-4:237.28811    Scaffold0001:8955
Scaffold0001    27702   28374   Chickpea-4:236.88576    Scaffold0001:27703
Scaffold0001    63097   63098   GeneticMap-lg0:135.93   Scaffold0001:63098
Scaffold0001    64515   67197   Chickpea-4:236.69478    Scaffold0001:64516
Scaffold0001    64596   64597   GeneticMap-lg0:135.93   Scaffold0001:64597
Scaffold0001    67504   69163   OpticalMap-OM_Chr1:294.56259    765.7   +       Scaffold0001:67505
Scaffold0001    69163   83408   OpticalMap-OM_Chr1:294.58039    765.7   +       Scaffold0001:69164
Scaffold0001    81629   86232   Chickpea-4:236.34549    Scaffold0001:81630
Scaffold0001    83408   85554   OpticalMap-OM_Chr1:294.71839    765.7   +       Scaffold0001:83409
Scaffold0001    84529   84530   GeneticMap-lg0:135.93   Scaffold0001:84530
```
This file can be used as input to ALLMAPS, starting from **Step 3** in the [ALLMAPS main walk-through tutorial](https://github.com/tanghaibao/jcvi/wiki/ALLMAPS).