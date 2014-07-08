## Introduction
Phenotyping plant organs (seeds, flowers, leaves, fruits, nuts) is important in Quantitative Trait Loci (QTL) mapping and whole genome association studies. Computerized phenotyping of digital images promises rapid, robust and reproducible typing of length and color traits. We highlight several features of GRABSEEDS.

* Work on seeds, leaves and flowers from a set of organisms
* Easy to use, command line interface, runs fast
* Robust against lighting condition, noisy background and overlapping seeds
* Text recognition towards full automation of seed phenotyping projects

## Installation
### External dependencies
* [ImageMagick](http://www.imagemagick.org/)
* [tesseract-ocr](https://code.google.com/p/tesseract-ocr/)

### Python dependencies
* [scipy](http://www.scipy.org/)
* [matplotlib](http://matplotlib.org/)
* [scikit-image](http://scikit-image.org/)
* [pypdf2](http://mstamy2.github.io/PyPDF2/)
* [wand](http://docs.wand-py.org/en/0.3.7/)
* [pytesseract](https://github.com/madmaze/python-tesseract)
* [colormath](https://github.com/gtaylor/python-colormath)
* [xlrd/xlwt](http://www.python-excel.org/)

Most of the Python libraries can be installed using `easy_install` or `pip install`, for example:
```
easy_install wand
```

Finally, make sure that you install [jcvi](https://github.com/tanghaibao/jcvi/) repository and put the directory on your `PYTHONPATH`. For example if you want to install it to `~/code`, do the following:
```
$ cd ~/code
$ git clone git@github.com:tanghaibao/jcvi.git
$ export PYTHONPATH=~/code:$PYTHONPATH
```
To avoid setting `PYTHONPATH` everytime, please insert the last command in your `.bashrc` or `.bash_profile`.

If you are somehow stuck, please check out [our installation guide in heavy details](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-install).

## Walk-through example

See more examples at the [GRABSEEDS Gallery](https://github.com/tanghaibao/jcvi/wiki/GRABSEEDS:-How-to-install).
