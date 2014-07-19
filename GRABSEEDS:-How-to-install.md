GRABSEEDS make heavy use of image manipulation and text recognition libraries and software. Please be patient and install the dependencies listed below. 

## External dependencies
* [ImageMagick](http://www.imagemagick.org/)
    - ``libmagickwand-dev`` for APT on Debian/Ubuntu
    - ``ImageMagick-devel`` for Yum on CentOS
* [tesseract-ocr](https://code.google.com/p/tesseract-ocr/)
    - Make sure that you can run ``tesseract`` on the command line (i.e. in your ``PATH``).

## Python dependencies
* [scipy](http://www.scipy.org/)
* [matplotlib](http://matplotlib.org/)
* [scikit-image](http://scikit-image.org/)
* [pypdf2](http://mstamy2.github.io/PyPDF2/)
* [wand](http://docs.wand-py.org/en/0.3.7/)
* [jcvi](https://github.com/tanghaibao/jcvi/)

Most of these Python libraries can be installed using `easy_install` or `pip install`, for example:
```
easy_install scipy matplotlib scikit-image pypdf2 wand jcvi
```
*Optional*: If you experience problem installing the jcvi library, you can clone the [jcvi](https://github.com/tanghaibao/jcvi/) repository directly and put the directory on your `PYTHONPATH`. For example, if you want to install it to `~/code`, do the following:
```
$ cd ~/code
$ git clone git://github.com/tanghaibao/jcvi.git
$ export PYTHONPATH=~/code:$PYTHONPATH
```
To avoid setting `PYTHONPATH` everytime, please insert the last command in your `.bashrc` or `.bash_profile`.

## Test
Download the test image below (right click then download).

![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/test.JPG)

Run the following command:
```
python -m jcvi.graphics.grabseeds seeds test.JPG
```
This command generates a PDF file for debugging purposes while all the identified objects will be written to stdout. If no error messages appear, congratulations! Now feel free to explore [GRABSEEDS](wiki/GRABSEEDS).