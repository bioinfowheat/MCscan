## Introduction

## Installation
### Python dependencies
* [Biopython](http://biopython.org/)
* [Numpy](numpy.org)
* [DEAP](https://code.google.com/p/deap/)
* [NetworkX](https://networkx.github.io/)
* [Matplotlib](matplotlib.org) (required only for plotting)
* [brewer2mpl](https://github.com/jiffyclub/brewer2mpl) (required only for plotting)
Most of the Python libraries can be installed using `easy_install` or `pip install`, for example:
```
easy_install deap
pip install deap
```

Finally, make sure that you install the `jcvi` repository and put the directory on your `PYTHONPATH`. For example if you want to install it to `~/code`, do the following:
```
$ cd ~/code
$ git clone git@github.com:tanghaibao/jcvi.git
$ export PYTHONPATH=~/code:$PYTHONPATH
```
To avoid setting `PYTHONPATH` everytime, please insert the last command in your `.bashrc` or `.bash_profile`.

### External dependencies
* concorde
* faSize
* liftOver

These external dependencies are available in my repo [jcvi-bin](https://github.com/tanghaibao/jcvi-bin), which is compiled on Linux (CentOS6). If they fail to execute, please get concorde from [here] (http://www.math.uwaterloo.ca/tsp/concorde.html), faSize/liftOver from [here](http://hgdownload.cse.ucsc.edu/admin/jksrc.zip) and recompile if necessary. 

## Walk-through example