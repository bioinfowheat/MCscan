Occasionally, there are chimeric contigs that map to multiple linkage groups or chromosomes. Such contigs are problematic and should be split prior to running ALLMAPS. There is no standard way of splitting chimeric contigs. Several questions exist:
- where do we make the splits?
- which map do we trust for making the splits?

Let's assume that we have a ``map.bed`` that we rely on to make the splits. First we want to identify the breakpoints, using the ``split`` command in ALLMAPS. Initially, we have the raw breakpoints coordinates in ``breakpoints.bed``. The parameter ``--chunk=4`` tells ALLMAPS to cut contigs where there are significant matches (at least 4 markers) to multiple linkage groups.

```console
$ python -m jcvi.assembly.allmaps split map.bed --chunk=4 > breakpoints.bed
```

These coordinates are very rough, of course. We could do better by intersecting with the gaps in the assembly, where most misjoins occur. The first command computes where the gaps are, the second command refines the breakpoint by finding the largest gap in the breakpoint region.

    $ python -m jcvi.formats.fasta gaps genome.fasta
    $ python -m jcvi.assembly.patch refine breakpoints.bed genome.gaps.bed

Once this is done, we create a new AGP file that specifies how to split the breakpoints exactly. Final command to rebuild the genome into ``genome.SPLIT.fasta``.

```console
$ python -m jcvi.formats.sizes agp genome.fasta
$ python -m jcvi.formats.agp mask genome.fasta.agp breakpoints.genome.refined.bed --splitobject --splitsingle
$ python -m jcvi.formats.agp build genome.fasta.masked.agp genome.fasta genome.SPLIT.fasta
```

Once we have obtained the new genome with chimeric contigs split (new contig identifiers will be generated, e.g. ``scaffold_1.1``, ``scaffold_1.2``). 

Now re-run your BLAST/marker mapping, so your new BED file contain the new identifiers after the split, and proceed with ALLMAPS.

## Additional notes

If you need to transfer the annotations onto the SPLIT assembly, you'll need to perform `liftOver`:

```console
$ cp genome.fasta.masked.agp genome_to_SPLIT.agp
$ python -m jcvi.formats.chain fromagp genome_to_SPLIT.agp genome.fasta genome.SPLIT.fasta
$ liftOver -gff genome.gff3 genome_to_SPLIT.chain SPLIT.gff3 unmapped
```

To sum up, we used the information from AGP to generate a `chain` file, which we can then use `liftOver` to convert coordinates.