Examples below show the command line usage of GRABSEEDS, in particular, how to set parameters for more accurate feature extractions.

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
Increase ``--kernel`` value to ensure that the objects are properly filled.
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/bluredges.png)

### Size filtering (``--minsize``, ``--maxsize``)
In the following example, the default settings identified a smaller object (ruler).
![](https://dl.dropboxusercontent.com/u/15937715/Data/GRABSEEDS/sizeselection0.png)

Use ``--minsize`` cutoff effectively removes the artifact, default ``--minsize=.05``, ``--maxsize=50`` which corresponds to any object with pixel counts that are between 0.05% to 50% of the entire photo. 

In the log file, we noticed the following information:
```
12:27:59 [grabseeds] Find objects with pixels between 270 (0.05%) and 270000 (50%)
12:27:59 [grabseeds] A total of 8 objects identified.
12:27:59 [grabseeds] Seed #1: 11618 pixels (676 sampled) - 2.15%
12:28:00 [grabseeds] Seed #2: 15956 pixels (1024 sampled) - 2.95%
12:28:00 [grabseeds] Seed #3: 14238 pixels (900 sampled) - 2.64%
12:28:00 [grabseeds] Seed #4: 14577 pixels (1024 sampled) - 2.70%
12:28:00 [grabseeds] Seed #5: 14639 pixels (1024 sampled) - 2.71%
12:28:00 [grabseeds] Seed #6: 13622 pixels (1156 sampled) - 2.52%
12:28:00 [grabseeds] Seed #7: 582 pixels (16 sampled) - 0.11%
12:28:00 [grabseeds] Seed #8: 342 pixels (36 sampled) - 0.06%
```
The command below changes the lower cutoff to ``1``, which is 1%, this cutoff would be able to trim off seed #7 and #8 that are false positives.
```
python -m jcvi.graphics.grabseeds seeds sizeselection.JPG --minsize=1
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