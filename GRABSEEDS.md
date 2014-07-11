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

To process single image, for example, ``test.JPG``, run this command:
```
python -m jcvi.graphics.grabseeds seeds test.JPG
```

This command generates a PDF file for debugging purposes while all the identified objects will be written to stdout. The PDF output contains object information layered onto original image. The four panels contain **Original picture**, **Edge detection**, **Object detection**, **Object list**, respectively. These panels are useful in finding the best parameter for object detection.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/test.png)

#### Noisy background (``--sigma``)
When the background is non-uniform and contain texture (for example, cloth), increase ``--sigma`` value (default sigma=1). For example, using the default sigma=1 identifies certain features that interfere with the real objects.
```
python -m jcvi.graphics.grabseeds seeds noisy.JPG
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/noisy0.png)
```
python -m jcvi.graphics.grabseeds seeds noisy.JPG --sigma=2
```
https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/noisy.png

#### Blur edges (``--kernel``)

#### Size filtering (``--minsize``, ``--maxsize``)

#### Overlapping seeds (``--watershed``)

#### Image cropping (``--rows``, ``--cols``)

#### Text label (``--labelrows``, ``--labelcols``)

#### Calibration (``calibrate``)

#### Batch mode (``batchseeds``)

## Gallery
See more examples at the [GRABSEEDS Gallery](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-Gallery).