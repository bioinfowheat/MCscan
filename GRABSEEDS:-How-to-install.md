###1. Install numpy
```
$ wget http://sourceforge.net/projects/numpy/files/NumPy/1.8.1/numpy-1.8.1.tar.gz
$ tar -zxvf numpy-1.8.1.tar.gz 
$ python setup.py build
$ python setup.py install --user
```
###2. Install matplotlib
```
$ wget https://downloads.sourceforge.net/project/matplotlib/matplotlib/matplotlib-1.3.1/matplotlib-1.3.1.tar.gz
$ tar -zxvf matplotlib-1.3.1.tar.gz
$ cd matplotlib-1.3.1
$ python setup.py build
$ python setup.py install --user
```
###3. Install brewer2mpl
```
download package from https://github.com/jiffyclub/brewer2mpl
$ unzip brewer2mpl-master.zip 
$ cd brewer2mpl-master/
$ python setup.py build
$ python setup.py install --user
```
###4. Install GRABSEEDS
```
$ mkdir code
$ cd code
$ git clone git@github.com:tanghaibao/jcvi.git       ###make sure you have github access
$ export PYTHONPATH=/home/username/code:$PYTHONPATH  ###make sure this code directory is on your PYTHONPATH
```
###5. Test sample data
