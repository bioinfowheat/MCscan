## Introduction
Phenotyping plant organs (seeds, flowers, leaves, fruits, nuts) is important in Quantitative Trait Loci (QTL) mapping and whole genome association studies. Computerized phenotyping of digital images promises rapid, robust and reproducible typing of length and color traits. We highlight several features of GRABSEEDS.

* Work on seeds, leaves and flowers from a set of organisms
* Easy to use, command line interface, runs fast
* Robust against lighting condition, noisy background and overlapping seeds
* Label recognition towards full automation of seed phenotyping projects

## Installation
Please check out [our installation guide](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-install).

## Single image processing
*Please note: the following image examples can be downloaded [here](https://www.dropbox.com/s/is4jrmlmmcfdpdp/test-data.zip).*

To process single image, for example, ``test.JPG``, run this command:
```
python -m jcvi.graphics.grabseeds seeds test.JPG
```
This command generates a PDF file for debugging purposes while all the identified objects will be written to stdout. The PDF output contains object information layered onto original image. The four panels contain **Original picture**, **Edge detection**, **Object detection**, **Object list**, respectively. These panels are useful in finding the best parameter for object detection.
![](https://www.dropbox.com/s/lmkc9j0qj4l37pm/test.png?raw=1)

### Image cropping (``--rows``, ``--cols``)
In the following example, the image contains a package that interfere with the seed recognition.
![](https://www.dropbox.com/s/1cb3mvk8npi67ja/label0.png?raw=1)

The solution is to extract certain rows (or columns) from original image, for example ``--rows=:800`` will extract the first 800 rows. Please use the coordinates on the axes of the image as a guide.
```
python -m jcvi.graphics.grabseeds seeds label.JPG --rows=:800
```
![](https://www.dropbox.com/s/xsv88ize4f0t177/label1.png?raw=1)

### Text label (``--labelrows``, ``--labelcols``)
To fully automate phenotyping pipeline, placing a text label next to the seeds is a good idea. The packaging in the previous example contains text. Similarly to cropping the image area, ``--labelrows=1200:`` tells the program to run OCR starting on row 1200. Consequently the following command extracts the label as *2013 Sh Backcross 401O2B*.
```
python -m jcvi.graphics.grabseeds seeds label.JPG --rows=:800 --labelrows=1200:
```
![](https://www.dropbox.com/s/xuc4ter7jitj132/label2.png?raw=1)

## Improve recognition accuracy
Please see more examples at [GRABSEEDS: How to tune accuracy](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-tune-accuracy).

## Calibration
If the photo sessions are long and separated on multiple days, it may be important to calibrate before each batch of images. The calibration serves several purposes:
- Calculate pixel-to-inch ratio, then the seed length and width can be converted to inch or cm
- Normalize the effect of lighting and correct the RGB code
- Yield stable results when camera settings, lighting, or lens-to-table distance varies

We recommend using a [ColorChecker](http://en.wikipedia.org/wiki/ColorChecker) to perform the calibration. You can make your own one by printing [this](https://www.dropbox.com/s/qthnnrq79hngf84/colorchecker.pdf) out, and then snap a picture of it. We recommend you later rename this file ``calibrate.JPG`` and place it alongside with the images of the same batch.

Now measure the individual boxes on the paper and record the size in squared cm units. Let's say the size is 1cm2. Your calibration command would be:
```
python -m jcvi.graphics.grabseeds calibrate batch/calibrate.JPG 1
```
![](https://www.dropbox.com/s/eo5kc4unluhour0/calibrate.png?raw=1)

It is important to check the PDF debugging file afterwards to make sure the calibration is done properly:
- It must properly identifies most of the 24 boxes
- Make sure to orient your ColorChecker correctly, not upside-down

The calibration generates a `calibrate.json` file, which you can then use to correct the size and color in the image processing. For example:
```
python -m jcvi.graphics.grabseeds seeds test.JPG --calibrate=batch/calibrate.json
```
For the curious mind, `calibrate.json` includes the pixel-cm-ratio and the RGB linear transform matrix used in color correction.
![](https://www.dropbox.com/s/ss8iv7xpm8zkb1l/formula.png?raw=1)
```
{
    "PixelCMratio": 200.0787345021954,
    "RGBtransform": [
        [
            1.1742493588325145,
            0.20095099843680092,
            -0.15413310306171132
        ],
        [
            0.10358271788195579,
            1.2457001216676802,
            -0.01189225811915571
        ],
        [
            0.11473041282784162,
            0.15234061501666415,
            1.1957963092234285
        ]
    ]
}
```

## Batch processing
Batch processing uses the same command interface as single image processing. However, instead of ``seeds``, use ``batchseeds``.

First, run the calibration if needed, note that you have to replace ``1`` with the real box size that you measured in the ColorChecker printout.
```
python -m jcvi.graphics.grabseeds calibrate batch/calibrate.JPG 1
```
Next, run the batch processing on a folder, for example, ``batch`` below. The batch processing will assume each image within this folder uses the same image processing (like cropping, de-noising). For example:
```
python -m jcvi.graphics.grabseeds batchseeds batch --rows=:800 --labelrows=1200:
```

The batch processing generates a combined PDF ``batch-output.pdf`` for visual debugging, and ``batch-output.tsv`` for the data in spreadsheet format.

![](https://www.dropbox.com/s/ihbby3j6im93k4s/screenshot.png?raw=1)

### Best practice for batch processing
We have a few recommendations with regards to large phenotyping experiments:
- Zoom onto the seeds and make them appear bigger in photo
- Avoid shadow at all cost and use a background with little texture (less noise). See a poor man's setup below.
- Store each batch of images in the same folder. Identify the calibration photo, and rename it as ``calibrate.JPG``.

![](https://www.dropbox.com/s/wcl5a3ii78ovgsz/poorman.jpg?raw=1)