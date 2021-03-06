# Table of contents
* [Dependencies](#dependencies)
* [Workflow](#workflow)
   - [Download data](#download-data)
   - [Pairwise synteny search](#pairwise-synteny-search)
   - [Pairwise synteny visualization](#pairwise-synteny-visualization)
   - [Macrosynteny visualization](#macrosynteny-visualization)
   - [Macrosynteny getting fancy](#macrosynteny-getting-fancy)
   - [Microsynteny visualization](#macrosynteny-visualization)
   - [Microsynteny getting fancy](#microsynteny-getting-fancy)

# Dependencies
Two external command-line tools are needed:
* [LASTAL](http://last.cbrc.jp). Compile and then move ``lastal`` and ``lastdb`` on your ``PATH``.
* Optional [SCIP](http://scip.zib.de/). You'll need the executable ``scip`` on your ``PATH``. You can also download a copy from [here](https://github.com/tanghaibao/jcvi-bin/tree/master/bin).

# Workflow
Let's assume that you want to perform synteny comparison between ``grape`` and ``peach``. This workflow is also available as an online Jupyter notebook (credits: Wayne Decatur [@fomightez](https://github.com/fomightez/mcscan-binder)).

[![Binder](http://mybinder.org/badge.svg)](http://beta.mybinder.org/v2/gh/fomightez/mcscan-binder/master?filepath=index.ipynb)

## Download data
For MCscan to work, we'll need sequences (``FASTA`` format) and coordinates (``BED`` format). For this example, let's get these data from [Phytozome](https://phytozome.jgi.doe.gov/pz/portal.html).

The first time you try to run the fetch command, the script will ask for your Phytozome login (`jcvi` will not store or send your Phytozome login anywhere).

    $ python -m jcvi.apps.fetch phytozome
    Phytozome Login: xxxxxxxx
    Phytozome Password:

If you don't have a login, register one [here](https://phytozome.jgi.doe.gov/pz/portal.html). Then you'll be able to see the current list of genomes.

    $ python -m jcvi.apps.fetch phytozome
    ...
                 ZmaysPH207                      Zmays
                    Zmarina                  Vvinifera
                   Vcarteri                  Tpratense
                     Tcacao                   Sviridis
                 Stuberosum                  Spurpurea
                 Spolyrhiza            Smoellendorffii
              Slycopersicum                   Sitalica
                    Sfallax                   Sbicolor
                  Rcommunis                  Pvulgaris
                  Pvirgatum               Ptrichocarpa
                   Ppersica                    Ppatens
                    Phallii                  Othomaeum
             OsativaKitaake                    Osativa
               Olucimarinus                Mtruncatula
                  MspRCC299           MpusillaCCMP1545
                Mpolymorpha                  Mguttatus
                 Mesculenta                 Mdomestica
    ...
    $ python -m jcvi.apps.fetch phytozome Vvinifera,Ppersica
    ...
    $ ls
    Ppersica_298_v2.1.cds.fa.gz              Vvinifera_145_Genoscope.12X.cds.fa.gz
    Ppersica_298_v2.1.gene.gff3.gz           Vvinifera_145_Genoscope.12X.gene.gff3.gz

Convert the GFF to BED file and rename them.

    $ python -m jcvi.formats.gff bed --type=mRNA --key=Name Vvinifera_145_Genoscope.12X.gene.gff3.gz -o grape.bed
    $ python -m jcvi.formats.gff bed --type=mRNA --key=Name Ppersica_298_v2.1.gene.gff3.gz -o peach.bed

I also like to reformat the Phytozome FASTA files as well.
    
    $ python -m jcvi.formats.fasta format Vvinifera_145_Genoscope.12X.cds.fa.gz grape.cds
    $ python -m jcvi.formats.fasta format Ppersica_298_v2.1.cds.fa.gz peach.cds

Okay! Now we are ready for the next step.

## Pairwise synteny search
We now have a clean set of files to proceed.

    $ ls *.???
    grape.bed grape.cds peach.bed peach.cds
    $ python -m jcvi.compara.catalog ortholog grape peach --no_strip_names
    23:34:43 [synteny] Assuming --qbed=grape.bed --sbed=peach.bed
    23:34:43 [base] Load file `grape.bed`
    23:34:43 [base] Load file `peach.bed`
    23:34:44 [blastfilter] Load BLAST file `grape.peach.last` (total 515965 lines)
    23:34:44 [base] Load file `grape.peach.last`
    23:34:46 [blastfilter] running the cscore filter (cscore>=0.70) ..
    23:34:46 [blastfilter] after filter (379462->50584) ..
    23:34:46 [blastfilter] running the local dups filter (tandem_Nmax=10) ..
    23:34:47 [blastfilter] after filter (50584->21696) ..
    ...
    Stats: Min=4 Max=1002 N=687 Mean=67.1863173216885 SD=110.64978224380248 Median=27.0 Sum=46157
    NR stats: Min=4 Max=356 N=687 Mean=26.595342066957787 SD=43.0459201862291 Median=11.0 Sum=18271

That's it! This calls LAST to do the comparison, filter the LAST output to remove tandem duplications and weak hits. A single linkage clustering is performed on the LAST output to cluster anchors into synteny blocks. At the end of the run, you'll see the summary statistics of the synteny blocks.

    $ ls grape.peach.*
    grape.peach.anchors        grape.peach.last.filtered  grape.peach.pdf
    grape.peach.last           grape.peach.lifted.anchors

The ``.last`` file is raw LAST output, ``.last.filtered`` is filtered LAST output, ``.anchors`` is the seed synteny blocks (high quality), ``.lifted.anchors`` recruits additional anchors to form the final synteny blocks.

Some might ask, what exactly is the `.anchors` file format? [See discussion here](https://github.com/tanghaibao/jcvi/issues/141).

## Pairwise synteny visualization
The best visualization to see pairwise synteny is using dot plot. This is just one command.

    $ python -m jcvi.graphics.dotplot grape.peach.anchors

We have the following dot plot in ``grape.peach.pdf``. 

![Grape-peach-synteny](https://www.dropbox.com/s/34hqbcstpf8hy1p/grape.peach.png?raw=1)

To understand what the dot plot says, look at either horizontally or vertically and count up how many blocks we generally see. Horizontally, we have for each peach region, up to 3 synteny regions in grape. Also vertically, we have for each grape region, up to 3 syntenic regions in peach. It helps to understand that grape and peach share a genome triplication event ("gamma") event, giving rise to this 3:3 pattern. 

If you look closely, one of the 3 regions are often stronger, which corresponds to the orthologous regions between the two genomes. What if we just want to get the 1:1 orthologous region? We'll just repeat what we did but adding an option ``--cscore=.99``. C-score is defined by the ratio of LAST hit to the best BLAST hits to either the query and hit. A C-score cutoff of .99 effectively filters the LAST hit to contain reciprocal best hit (RBH).

    $ rm grape.peach.last.filtered 
    $ python -m jcvi.compara.catalog ortholog grape peach --cscore=.99 --no_strip_names
    $ python -m jcvi.graphics.dotplot grape.peach.anchors

![Grape-peach-synteny-.99](https://www.dropbox.com/s/32mmo0kfhtx5b8g/grape.peach.cscore.99.png?raw=1)

We could also quick test if the synteny pattern is indeed **1:1**, by running:

    $ python -m jcvi.compara.synteny depth --histogram grape.peach.anchors

![Grape-peach-depth](https://www.dropbox.com/s/hhx2dtryrum2gyo/grape.peach.depth.png?raw=1)

## Macrosynteny visualization
Now let's move to a different kind of visualization using the same synteny output ``grape.peach.anchors``. Aside from the BED files and synteny files we already have, we need to prepare two additional files. 

First is the ``seqids`` file, which tells the plotter which set of chromosomes to include. Here, we've removed unplaced and small scaffolds. The first line contains 19 grape chromosomes and second line contains 8 peach chromosomes.

    chr1,chr2,chr3,chr4,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr16,chr17,chr18,chr19
    Pp01,Pp02,Pp03,Pp04,Pp05,Pp06,Pp07,Pp08

Second is the ``layout`` file, which tells the plotter where to draw what. The whole canvas is 0-1 on x-axis and 0-1 on y-axis. First, three columns specify the position of the track. Then rotation, color, label, vertical alignment (`va`), and then the genome BED file. Track 0 is now grape, track 1 is now peach. The next stanza specifies what edges to draw between the tracks. ``e, 0, 1`` asks to draw edges between track 0 and 1, using information from the ``.simple`` file. 

    # y, xstart, xend, rotation, color, label, va,  bed
     .6,     .1,    .8,       0,      , Grape, top, grape.bed
     .4,     .1,    .8,       0,      , Peach, top, peach.bed
    # edges
    e, 0, 1, grape.peach.anchors.simple

Finally this command generates a ``.simple`` file which is a more succinct form than the ``.anchors`` file.

    $ python -m jcvi.compara.synteny screen --minspan=30 --simple grape.peach.anchors grape.peach.anchors.new 

With all input files ready, let's plot.

    $ python -m jcvi.graphics.karyotype seqids layout

This generates our karyotype figure.

![Grape-peach-karyotype](https://www.dropbox.com/s/51k8jujyzage3oa/grape.peach.karyotype.png?raw=1)

Further customization is possible. For example, change the order of chromosomes in ``seqids`` could lead to a visually more appealing figure. Also, play with the positions, color, labels etc in the ``layout`` file.

What if we want to highlight a specific block? We should go into the ``.simple`` file, locate the relevant block. Please note that each line in the ``.simple`` file is a synteny blocks, with start and stop grape gene, then start and stop peach gene, final two columns are score and orientation.

    $ vi grape.peach.anchors.simple 
    GSVIVT01012228001	GSVIVT01012173001	Prupe.1G281700.1	Prupe.1G288300.1	72	-
    g*GSVIVT01012028001	GSVIVT01000604001	Prupe.1G299800.1	Prupe.1G340600.1	518	+
    GSVIVT01000603001	GSVIVT01000557001	Prupe.1G239100.1	Prupe.1G242900.2	51	+    
    ...
    (save the file)
    $ python -m jcvi.graphics.karyotype seqids layout

We have then highlighted (with color 'g' green) a particular synteny block in the figure.
![Grape-peach-karyotype-green](https://www.dropbox.com/s/so2td0eqklbklnl/grape.peach.karyotype-green.png?raw=1)

We can also have alternative shade style:

    $ python -m jcvi.graphics.karyotype seqids layout --shadestyle=line

Note that the shade now turns into lines instead of the Bezier curve before.

![Grape-peach-karyotype-line](https://www.dropbox.com/s/vvql9yd4k6ihzjt/grape.peach.karyotype-line.png?raw=1)

## Macrosynteny getting fancy
We can be really creative about karyotype plots! For a start, we can add as many genomes as we want. Let's add cacao. We just need to repeat the downloading and formatting on the cacao genome (extract `cacao.cds` and `cacao.bed`).

    $ python -m jcvi.apps.fetch phytozome Tcacao
    $ python -m jcvi.formats.gff bed --type=mRNA --key=Name Tcacao_233_v1.1.gene.gff3.gz -o cacao.bed
    $ python -m jcvi.formats.fasta format Tcacao_233_v1.1.cds.fa.gz cacao.cds

Say we are interested in looking at peach and cacao comparisons. 

    $ python -m jcvi.compara.catalog ortholog peach cacao --cscore=.99 --no_strip_names
    $ python -m jcvi.compara.synteny screen --minspan=30 --simple peach.cacao.anchors peach.cacao.anchors.new

Now that we have `cacao.bed` and `peach.cacao.anchors.simple`, we'll need to add them to the `layout`. Please note the two new lines - line 4 and 7. Section `# edges` says that we should connect track 0 (grape) with 1 (peach), track 1 (peach) with 2 (cacao).

    # y, xstart, xend, rotation, color, label, va,  bed
     .7,     .1,    .8,      15,      , Grape, top, grape.bed
     .5,     .1,    .8,       0,      , Peach, top, peach.bed
     .3,     .1,    .8,     -15,      , Cacao, bottom, cacao.bed
    # edges
    e, 0, 1, grape.peach.anchors.simple
    e, 1, 2, peach.cacao.anchors.simple

Remember to add the major cacao chromosomes (line 3) in `seqids`. Remember the order of the genomes in `seqids` need to match the order in `layout`.

    chr1,chr2,chr3,chr4,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr16,chr17,chr18,chr19
    scaffold_1,scaffold_2,scaffold_3,scaffold_4,scaffold_5,scaffold_6,scaffold_7,scaffold_8
    scaffold_1,scaffold_2,scaffold_3,scaffold_4,scaffold_5,scaffold_6,scaffold_7,scaffold_8,scaffold_9,scaffold_10r

We can also customize the rotation (in degrees), so we are no longer limited to the boring horizontal arrangement of chromosomes. After the command:

    $ python -m jcvi.graphics.karyotype seqids layout

We have the final product!
![Grape-peach-cacao-fancy](https://www.dropbox.com/s/9vl3ys3ndvimg4c/grape-peach-cacao.png?raw=1)

## Microsynteny visualization
What if we want to look at **local** synteny? Local synteny mostly focuses on gene-level, showing matching regions along with aligned gene models. For this, we'll need to compute the layout for the gene-level matchings:

    $ python -m jcvi.compara.synteny mcscan grape.bed grape.peach.lifted.anchors --iter=1 -o grape.peach.i1.blocks

Note that the option ``--iter=1`` says we are extracting *one* best region matching each grape region. If you take a look at the resulting ``grape.peach.i1.blocks`` file, it contains grape as the first column and peach as the second column. If the option ``--iter`` is set to 2, there will be 2 peach regions, and so on. Specifically, this will be useful to plot regions resulted from genome duplications. 

OK. The file ``grape.peach.i1.blocks`` contains lots of local regions! We can choose any arbitrary region to visualize.

    $ head -50 grape.peach.i1.blocks > blocks

Finally, we'll need a layout file just like in the macro-synteny plots. The ``blocks.layout`` file has:

    # x,   y, rotation,   ha,     va,   color, ratio,            label
    0.5, 0.6,        0, left, center,       m,     1,       grape Chr1
    0.5, 0.4,        0, left, center, #fc8d62,     1, peach scaffold_1
    # edges
    e, 0, 1

With command below, we can generate a local synteny plot:

    $ cat grape.bed peach.bed > grape_peach.bed
    $ python -m jcvi.graphics.synteny blocks grape_peach.bed blocks.layout

The resulting plot looks like below.
![Grape-peach-blocks](https://www.dropbox.com/s/ix2msddsndwe2ve/grape.peach.blocks.png?raw=1)

Similarly, we can also have alternative shade style:

    $ python -m jcvi.graphics.synteny blocks grape_peach.bed blocks.layout --shadestyle=line

![Grape-peach-blocks-line](https://www.dropbox.com/s/sheaenqyhkd5bbv/Grape-peach-blocks-line.png?raw=1)

## Microsynteny getting fancy
It is also possible to do more than two matching regions! Similar to the macro-synteny plots, first construct multi-synteny blocks using ``python -m jcvi.compara.synteny mcscan``, then modify the ``blocks.layout`` file to indicate more regions as well as edges between the regions.

Let's continue with our example with grape, peach, and cacao. Now let's say we want to build a ``blocks`` file with three genomes. It would be nice to have a single 'reference', let's pick grape for example, and align peach and cacao separately to grape. We already did peach, now similarly on cacao.

    $ python -m jcvi.compara.catalog ortholog grape cacao --cscore=.99
    $ python -m jcvi.compara.synteny mcscan grape.bed grape.cacao.lifted.anchors --iter=1 -o grape.cacao.i1.blocks

Time to combine ``blocks`` with this command.

    $ python -m jcvi.formats.base join grape.peach.i1.blocks grape.cacao.i1.blocks --noheader | cut -f1,2,4,6 > grape.blocks
    $ head -50 grape.blocks > blocks2

Take a look at this file:

    GSVIVT01012261001	.	.
    GSVIVT01012259001	.	.
    GSVIVT01012258001	.	.
    GSVIVT01012257001	.	.
    GSVIVT01012255001	Prupe.1G290900.1	Thecc1EG011472t1
    GSVIVT01012253001	Prupe.1G290800.2	Thecc1EG011473t1
    GSVIVT01012252001	Prupe.1G290700.1	Thecc1EG011474t1
    GSVIVT01012250001	Prupe.1G290600.1	Thecc1EG011475t1
    GSVIVT01012249001	Prupe.1G290500.1	Thecc1EG011478t1
    GSVIVT01012248001	Prupe.1G290400.1	Thecc1EG011482t1

This is awesome. Now we are almost ready. Our new layout file ``blocks2.layout`` says:

    # x,   y, rotation,     ha,     va, color, ratio,            label
    0.5, 0.6,        0, center,    top,      ,     1,       grape Chr1
    0.3, 0.4,        0, center, bottom,      ,    .5, peach scaffold_1
    0.7, 0.4,        0, center, bottom,      ,    .5, cacao scaffold_2
    # edges
    e, 0, 1
    e, 0, 2

Note we have changed the ``ratio`` for peach and cacao to ``.5`` to let them fit on the canvas. For publication-quality figures, you'll most likely need to tweak this layout file. Like the layout file in the macro-synteny section, edges stanza say connecting grape (column 0) with peach (column 1) and grape (column 0) with cacao (column 2). Now we run:
    
    $ cat grape.bed peach.bed cacao.bed > grape_peach_cacao.bed
    $ python -m jcvi.graphics.synteny blocks2 grape_peach_cacao.bed blocks2.layout

![Grape-peach-cacao-blocks](https://www.dropbox.com/s/imdou9ukntt4tam/grape-peach-cacao-blocks.png?raw=1)