All inter-scaffold gaps were configured to be a fixed size during the genome release. Some user may wish to get an estimate of the gap sizes. Accurate inference of gap size is dependent on the conversion between the map distances to the physical distances. In the case of genetic maps, this conversion ratio is known as the recombination rate, commonly measured in centiMorgan per Mb (cM / Mb). The recombination rates are widely variable between different genetic backgrounds, between chromosomes in the same genome, and even between different regions on the same chromosome. ALLMAPS uses a local estimation method based on the positions of the input markers. Cubic spline, which was suggested to be the best method to estimate recombination rate, is used to generate interpolation of genetic distance along the chromosome. Recombination rate curve is estimated by taking the derivative of the cubic spline. For each pair of markers that spans a gap, we converted the genetic distance to the physical distance based on the interpolated recombination rate, and then deducted the overhangs (distance from the markers to the edge of the scaffold) to infer the gap sizes. 

![Algorithm for estimating gap sizes](https://www.dropbox.com/s/pznqycut2ucfxjz/estimategaps.png?raw=1)

ALLMAPS provides the gap estimation method as an optional step (`estimategaps`) after the key steps were completed.
```
python -m jcvi.assembly.allmaps estimategaps JM-2.bed
```

This generates a new AGP file `JM-2.estimategaps.agp` which you can replace `JM-2.chr.agp`. Then rebuild the genome release.
```
mv JM-2.estimategaps.agp JM-2.chr.agp
python -m jcvi.assembly.allmaps build JM-2.bed scaffolds.fasta
```