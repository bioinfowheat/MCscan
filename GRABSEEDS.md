## Introduction
Phenotyping plant organs (seeds, flowers, leaves, fruits, nuts) is important in Quantitative Trait Loci (QTL) mapping and whole genome association studies. Computerized phenotyping of digital images promises rapid, robust and reproducible typing of length and color traits. We highlight several features of GRABSEEDS.

* Work on seeds, leaves and flowers from a set of organisms
* Easy to use, command line interface, runs fast
* Robust against lighting condition, noisy background and overlapping seeds
* Label recognition towards full automation of seed phenotyping projects

## Installation
Please check out [our installation guide](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-install).

## Single image processing
*Please note: the following image examples can be downloaded [here](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/test-data.zip).*

To process single image, for example, ``test.JPG``, run this command:
```
python -m jcvi.graphics.grabseeds seeds test.JPG
```
This command generates a PDF file for debugging purposes while all the identified objects will be written to stdout. The PDF output contains object information layered onto original image. The four panels contain **Original picture**, **Edge detection**, **Object detection**, **Object list**, respectively. These panels are useful in finding the best parameter for object detection.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/test.png)

### Image cropping (``--rows``, ``--cols``)
In the following example, the image contains a package that interfere with the seed recognition.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/label0.png)
The solution is to extract certain rows (or columns) from original image, for example ``--rows=:800`` will extract the first 800 rows. Please use the coordinates on the axes of the image as a guide.
```
python -m jcvi.graphics.grabseeds seeds label.JPG --rows=:800
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/label1.png)

### Text label (``--labelrows``, ``--labelcols``)
To fully automate phenotyping pipeline, placing a text label next to the seeds is a good idea. The packaging in the previous example contains text. Similarly to cropping the image area, ``--labelrows=1200:`` tells the program to run OCR starting on row 1200. Consequently the following command extracts the label as *2013 Sh Backcross 401O2B*.
```
python -m jcvi.graphics.grabseeds seeds label.JPG --rows=:800 --labelrows=1200:
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/label2.png)

## Improve recognition accuracy
Please see more examples at [GRABSEEDS: How to tune accuracy](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-tune-accuracy).

## Batch processing
### Calibration (``calibrate``)
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/calibrate.png)

### Batch mode (``batchseeds``)

### Best practice for batch processing