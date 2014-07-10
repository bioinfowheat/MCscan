GRABSEEDS make heavy use of image manipulation and text recognition libraries and software. Please be patient and install the dependencies listed below. 

### External dependencies
* [ImageMagick](http://www.imagemagick.org/)
* [tesseract-ocr](https://code.google.com/p/tesseract-ocr/)

### Python dependencies
* [scipy](http://www.scipy.org/)
* [matplotlib](http://matplotlib.org/)
* [brewer2mpl](https://github.com/jiffyclub/brewer2mpl)
* [scikit-image](http://scikit-image.org/)
* [pypdf2](http://mstamy2.github.io/PyPDF2/)
* [wand](http://docs.wand-py.org/en/0.3.7/)
* [colormath](https://github.com/gtaylor/python-colormath)
* [xlrd](http://www.python-excel.org/)
* [xlwt](http://www.python-excel.org/)

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