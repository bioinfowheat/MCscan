Examples below show the command line usage of GRABSEEDS, in particular, how to set parameters for more accurate feature extractions.

*Please note: the following image examples can be downloaded [here](https://www.dropbox.com/s/is4jrmlmmcfdpdp/test-data.zip).*

### Noisy background (``--sigma``)
In the following example, the default settings identified certain features that interfere with the real objects.
![](https://www.dropbox.com/s/uuugd17c9rq5ypa/noisy0.png?raw=1)

When the background is non-uniform and contain texture (for example, cloth), increase ``--sigma`` value. 
```
python -m jcvi.graphics.grabseeds seeds noisy.JPG --sigma=2
```
![](https://www.dropbox.com/s/w3uny2rmde6vx1p/noisy.png?raw=1)

### Blur edges (``--kernel``)
In the following example, the default settings correctly identified the objects but not the entire region, so the area calculation will be inaccurate. 
![](https://www.dropbox.com/s/fu7bv728m3gm949/bluredges0.png?raw=1)

Increase ``--kernel`` value to ensure that the objects are properly filled.
![](https://www.dropbox.com/s/yu9ehsi6sqifuaa/bluredges.png?raw=1)

### Size filtering (``--minsize``, ``--maxsize``)
In the following example, the default settings identified a smaller object (ruler).
![](https://www.dropbox.com/s/39qg8ywwc7j2eed/sizeselection0.png?raw=1)

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
![](https://www.dropbox.com/s/ba7o8azsgn3vryq/sizeselection.png?raw=1)

### Overlapping seeds (``--watershed``)
In the following example, the shadow connect the seeds into one giant object.
![](https://www.dropbox.com/s/c8ue53xz4vfel1f/touching0.png?raw=1)

Use ``--watershed`` to run the watershed algorithm to separate overlapping objects.
```
python -m jcvi.graphics.grabseeds seeds touching.JPG --watershed
```
![](https://www.dropbox.com/s/yh5tjot8syva54p/touching.png?raw=1)