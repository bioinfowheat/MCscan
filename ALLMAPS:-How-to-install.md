Please use the following if the quick installation method does not work for you.

###1. Install Biopython
```
$ wget http://biopython.org/DIST/biopython-1.63.tar.gz
$ tar -zxvf biopython-1.63.tar.gz
$ cd biopython-1.63
$ python setup.py build
$ python setup.py test
$ python setup.py install --user
```

###2. Install numpy
```
$ wget http://sourceforge.net/projects/numpy/files/NumPy/1.8.1/numpy-1.8.1.tar.gz
$ tar -zxvf numpy-1.8.1.tar.gz 
$ python setup.py build
$ python setup.py install --user
```

###3. Install DEAP
```
$ wget https://pypi.python.org/packages/source/d/deap/deap-1.0.1.tar.gz
$ tar -zxvf deap-1.0.1.tar.gz
$ cd deap-1.0.1
$ python setup.py install --user
```

###4. Install NetworkX
```
$ wget https://pypi.python.org/packages/source/n/networkx/networkx-1.8.1.tar.gz
$ tar -zxvf networkx-1.8.1.tar.gz
$ cd networkx-1.8.1/
$ python setup.py build
$ python setup.py install --user
```

###5. Install matplotlib
```
$ wget https://downloads.sourceforge.net/project/matplotlib/matplotlib/matplotlib-1.3.1/matplotlib-1.3.1.tar.gz
$ tar -zxvf matplotlib-1.3.1.tar.gz
$ cd matplotlib-1.3.1
$ python setup.py build
$ python setup.py install --user
```

###6. Install JCVI dependencies: concorde, faSize and liftOver
####6.1 From github
```
$ git clone git://github.com/tanghaibao/jcvi-bin.git
$ cp jcvi-bin/bin/* /usr/local/bin                  # /usr/local/bin is your own PATH
```
####6.2 Recompile
If github does not work for you, please get concorde, faSize/liftOver and recompile manually.
````
# recompile concorde 
$ wget http://www.math.uwaterloo.ca/tsp/concorde/downloads/codes/linux24/concorde.gz
$ gunzip concorde.gz
$ chmod u+x concorde
$ cp concorde /TO/YOUR/PATH

# recompile faSize/liftOver
$ wget http://hgdownload.cse.ucsc.edu/admin/jksrc.zip
$ unzip jksrc.zip
$ cd kent
$ uname -m                          # check your MACHTYPE
$ MACHTYPE=i686                     # for example, your MACHTYPE is i686
$ export MACHTYPE                   
$ mkdir ~/bin/$MACHTYPE             # kent apps will go to this directory. if you don't have it, make one
$ mkdir src/lib/$MACHTYPE           # if you don't have it, make another one in src/lib
$ cd src                            
$ make                              
$ ls -all ~/bin/i686                # check do you have faSize and liftOver
$ export PATH=~/bin/i686/:$PATH     # make sure that ~/bin/i686 is on your path
$ export PATH=~/bin                 # make sure that ~/bin is on your path as well
```

###7. Install ALLMAPS 
```
$ mkdir code
$ cd code
$ git clone git://github.com/tanghaibao/jcvi.git     
$ export PYTHONPATH=/home/username/code:$PYTHONPATH  # make sure the directory is on your PYTHONPATH
```

###8. Test sample data
```
$ wget https://dl.dropboxusercontent.com/u/15937715/Data/ALLMAPS-testdata/ALLMAPS-testdata.zip
$ unzip ALLMAPS-testdata.zip
$ cd ALLMAPS-testdata
$ sh run.sh
```