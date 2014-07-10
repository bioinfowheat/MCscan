## Introduction
Phenotyping plant organs (seeds, flowers, leaves, fruits, nuts) is important in Quantitative Trait Loci (QTL) mapping and whole genome association studies. Computerized phenotyping of digital images promises rapid, robust and reproducible typing of length and color traits. We highlight several features of GRABSEEDS.

* Work on seeds, leaves and flowers from a set of organisms
* Easy to use, command line interface, runs fast
* Robust against lighting condition, noisy background and overlapping seeds
* Text recognition towards full automation of seed phenotyping projects

## Installation
Please check out [our installation guide](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-install).

## Walk-through examples
Examples below show the command line usage of GRABSEEDS, in particular, how to set parameters for more accurate feature extractions.

#### Default

#### Noisy background (``--sigma``)

#### Blur edges (``--kernel``)

#### Size filtering (``--minsize``, ``--maxsize``)

#### Overlapping seeds (``--watershed``)

#### Image cropping (``--rows``, ``--cols``)

#### Text label (``--labelrows``, ``--labelcols``)

#### Batch mode (``batchseeds``)

## Gallery
See more examples at the [GRABSEEDS Gallery](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-Gallery).
