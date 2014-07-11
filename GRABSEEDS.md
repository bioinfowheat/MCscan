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

### Noisy background (``--sigma``)
In the following example, the default settings identified certain features that interfere with the real objects.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/noisy0.png)
When the background is non-uniform and contain texture (for example, cloth), increase ``--sigma`` value. 
```
python -m jcvi.graphics.grabseeds seeds noisy.JPG --sigma=2
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/noisy.png)

### Blur edges (``--kernel``)
In the following example, the default settings correctly identified the objects but not the entire region, so the area calculation will be inaccurate. 
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/bluredges0.png)
Consider increase ``--kernel`` value to ensure that the objects are properly filled.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/bluredges.png)

### Size filtering (``--minsize``, ``--maxsize``)
In the following example, the default settings identified a smaller object (ruler).
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/sizeselection0.png)
Use ``--minsize`` cutoff effectively removes the artifact, default ``minsize=0.0005``, ``maxsize=0.5`` which corresponds to any object with pixel counts that are between 0.05% to 50% of the entire photo.
```
python -m jcvi.graphics.grabseeds seeds sizeselection.JPG --minsize=.01
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/sizeselection.png)

### Overlapping seeds (``--watershed``)
In the following example, the shadow connect the seeds into one giant object.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/touching0.png)
Use ``--watershed`` to run the watershed algorithm to separate overlapping objects.
```
python -m jcvi.graphics.grabseeds seeds touching.JPG --watershed
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/touching.png)

### Image cropping (``--rows``, ``--cols``)
In the following example, the image contains a package that interfere with the seed recognition.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/label0.png)
The solution is to extract certain rows (or columns) from original image, for example ``--rows=:800`` will extract the first 800 rows. Please use the coordinates on the axes of the image as a guide.
```
python -m jcvi.graphics.grabseeds seeds label.JPG --rows=:800
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/label1.png)

### Text label (``--labelrows``, ``--labelcols``)
To fully automate phenotyping pipeline, placing a text label next to the seeds is a good idea. The packaging in the previous example contains text. Similarly to cropping the image area, ``--labelrows=1200:`` tells the program to run OCR starting on row 1200.
```
python -m jcvi.graphics.grabseeds seeds label.JPG --rows=:800 --labelrows=1200:
```
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/label2.png)

### Calibration (``calibrate``)
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/calibration.png)

### Batch mode (``batchseeds``)

## Gallery
See more examples at the [GRABSEEDS Gallery](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-Gallery).