# Dependencies
Two external command line tools are needed:
* [LASTAL](http://last.cbrc.jp). Compile and then move ``lastal`` and ``lastdb`` on your ``PATH``.
* Optional [SCIP](http://scip.zib.de/). You'll need the executable ``scip`` on your ``PATH``. You can also download a copy from [here](https://github.com/tanghaibao/jcvi-bin/tree/master/bin).

# Workflow
Let's assume that you want to perform synteny comparison between ``grape`` and ``peach``.

## Download data
For MCscan to work, we'll need sequences (``FASTA`` format) and coordinates (``BED`` format). For this example, let's get these data from [Phytozome](https://phytozome.jgi.doe.gov/pz/portal.html).

    $ python -m jcvi.apps.fetch phytozome
    ...
             Acoerulea               Alyrata             Athaliana
           Bdistachyon                 Brapa           Cclementina
               Cpapaya          Creinhardtii              Crubella
              Csativus             Csinensis Csubellipsoidea_C-169
              Egrandis                Fvesca                  Gmax
            Graimondii        Lusitatissimum            Mdomestica
            Mesculenta             Mguttatus     Mpusilla_CCMP1545
       Mpusilla_RCC299           Mtruncatula          Olucimarinus
               Osativa               Ppatens              Ppersica
          Ptrichocarpa             Pvirgatum             Pvulgaris
             Rcommunis              Sbicolor              Sitalica
         Slycopersicum       Smoellendorffii            Stuberosum
                Tcacao            Thalophila              Vcarteri
             Vvinifera                 Zmays         early_release
    ...
    $ python -m jcvi.apps.fetch phytozome Vvinifera,Ppersi
    ...
    $ ls
    Ppersica_139_cds.fa.gz  Ppersica_139_gene.gff3.gz  Vvinifera_145_cds.fa.gz  Vvinifera_145_gene.gff3.gz

Convert the GFF to BED file and rename them.

    $ python -m jcvi.formats.gff bed --type=mRNA --key=Name Vvinifera_145_gene.gff3.gz -o grape.bed
    $ python -m jcvi.formats.gff bed --type=mRNA --key=Name Ppersica_139_gene.gff3.gz -o peach.bed

I also like to clean headers to remove description fields from Phytozome FASTA files.
    
    $ python -m jcvi.formats.fasta format --sep="|" Vvinifera_145_cds.fa.gz grape.cds
    $ python -m jcvi.formats.fasta format --sep="|" Ppersica_139_cds.fa.gz peach.cds

Okay! Now we are ready for the next step.

## Pairwise synteny search
We now have a clean set of files to proceed.

    $ ls *.???
    grape.cds  peach.cds  grape.bed  peach.bed
    $ python -m jcvi.compara.catalog ortholog grape peach
    20:33:42 [base] lastdb peach peach.cds
    20:34:13 [base] lastal -u 0 -P 64 -i3G -f BlastTab peach grape.cds >grape.peach.last
    20:34:30 [synteny] Assuming --qbed=grape.bed --sbed=peach.bed
    20:34:31 [blastfilter] Load BLAST file `grape.peach.last` (total 403868 lines)
    20:34:31 [base] Load file `grape.peach.last`
    20:34:38 [blastfilter] running the cscore filter (cscore>=0.70) ..
    20:34:39 [blastfilter] after filter (294217->31229) ..
    20:34:39 [blastfilter] running the local dups filter (tandem_Nmax=10) ..
    20:34:39 [blastfilter] after filter (31229->21089) ..
    ...
    A total of 30654 (NR:18661) anchors found in 678 clusters.
    Stats: Min=4 Max=683 N=678 Mean=45.2123893805 SD=80.8407828815 Median=16.0 Sum=30654
    NR stats: Min=4 Max=460 N=678 Mean=27.5235988201 SD=48.0461479616 Median=11.0 Sum=18661

That's it! This calls LAST to do the comparison, filter the LAST output to remove tandem duplications and weak hits. A single linkage clustering is performed on the LAST output to cluster anchors into synteny blocks. At the end of the run, you'll see summary statistics of the synteny blocks.

    $ ls grape.peach.*
    grape.peach.lifted.anchors  grape.peach.anchors  grape.peach.last.filtered  grape.peach.last

The ``.last`` file is raw LAST output, ``.last.filtered`` is filtered LAST output, ``.anchors`` is the seed synteny blocks (high quality), ``.lifted.anchors`` recruits additional anchors to form the final synteny blocks.

## Visualize pairwise synteny
The best visualization to see pairwise synteny is using dotplot. This is just one command.

    $ python -m jcvi.graphics.dotplot grape.peach.anchors

We have the following dot plot in ``grape.peach.pdf``. 

![Grape-peach-synteny](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape.peach.png)

To understand what the dot plot says, look at either horizontally or vertically and count up how many blocks we generally see. Horizontally, we have for each peach region, up to 3 synteny regions in grape. Also vertically, we have for each grape region, up to 3 syntenic regions in peach. It helps to understand that grape and peach shares a genome triplication event ("gamma") event, giving rise to this 3:3 pattern. 

If you look closely, one of the 3 regions are often stronger, which corresponds to the orthologous regions between the two genomes. What if we just want to get the 1:1 orthologous region? We'll just repeat what we did but adding an option ``--cscore=.99``. C-score is defined by the ratio of LAST hit to the best BLAST hits to either the query and hit. A C-score cutoff of .99 effectively filters the LAST hit to contain reciprocal best hit (RBH).

    $ rm grape.peach.last.filtered 
    $ python -m jcvi.compara.catalog ortholog grape peach --cscore=.99
    $ python -m jcvi.graphics.dotplot grape.peach.anchors

![Grape-peach-synteny-.99](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape.peach.cscore.99.png)

We could also quick test if the synteny pattern is indeed **1:1**, by running:

    $ python -m jcvi.compara.synteny depth --histogram grape.peach.anchors

![Grape-peach-depth](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape.peach.depth.png)

## Macrosynteny visualization
Now let's move to a different kind of visualization using the same synteny output ``grape.peach.anchors``. Aside from the BED files and synteny files we already have, we need to prepare two additional files. 

First is the ``seqids`` file, which tells the plotter which set of chromosomes to include. Here, we've removed unplaced and small scaffolds. First line contains 19 grape chromosomes and second line contains 8 peach chromosomes.

    chr1,chr2,chr3,chr4,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr16,chr17,chr18,chr19
    scaffold_1,scaffold_2,scaffold_3,scaffold_4,scaffold_5,scaffold_6,scaffold_7,scaffold_8

Second is the ``layout`` file, which tells the plotter where to draw what. The whole canvas is 0-1 on x-axis and 0-1 on y-axis. First three columns specify the position of the track. Then rotation, color, label, vertical alignment (va), and then the genome BED file. Track 0 is now grape, track 1 is now peach. The next stanza specify what edges to draw between the tracks. ``e, 0, 1`` asks to draw edges between track 0 and 1, using information from the ``.simple`` file. 

    # y, xstart, xend, rotation, color, label, va,  bed
     .6,     .1,    .8,       0,      , Grape, top, grape.bed
     .4,     .1,    .8,       0,      , Peach, top, peach.bed
    # edges
    e, 0, 1, grape.peach.anchors.simple

Finally this command generates a ``.simple`` file which is a more succinct form than ``.anchors`` file.

    $ python -m jcvi.compara.synteny screen --minspan=30 --simple grape.peach.anchors grape.peach.anchors.new 

With all input files ready, let's plot.

    $ python -m jcvi.graphics.karyotype seqids layout

This generates our karyotype figure.

![Grape-peach-karyotype](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape.peach.karyotype.png)

Further customization is possible. For example, change the order of chromosomes in ``seqids`` could lead to a visually more appealing figure. Also, play with the positions, color, labels etc in the ``layout`` file.

What if we want to highlight a specific block? We should go into the ``.simple`` file, locate the relevant block. Please note that each line in the ``.simple`` file is a synteny blocks, with start and stop grape gene, then start and stop peach gene, final two columns are score and orientation.

    $ vi grape.peach.anchors.simple 
    g*GSVIVT01012028001 GSVIVT01000604001   ppa011886m  ppa008534m  392 +
    GSVIVT01010441001   GSVIVT01000970001   ppa022891m  ppa001358m  115 -
    GSVIVT01000555001   GSVIVT01003228001   ppa002809m  ppa010569m  359 +
    ...
    (save the file)
    $ python -m jcvi.graphics.karyotype seqids layout

We have then highlighted (with color 'g' green) a particular synteny block in the figure.
![Grape-peach-karyotype-green](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape.peach.karyotype-green.png)

## Getting fancy
We can be really creative about karyotype plots! For a start, we can add as many genomes as we want. Let's add cacao. We just need to repeat the downloading and formatting on the cacao genome (extract `cacao.cds` and `cacao.bed`).

    $ python -m jcvi.apps.fetch phytozome Tcacao
    $ python -m jcvi.formats.fasta format --sep="|" Tcacao_233_cds.fa.gz cacao.cds
    $ python -m jcvi.formats.gff bed --type=mRNA --key=Name Tcacao_233_gene.gff3.gz -o cacao.bed

Say we are interested in looking at peach and cacao comparisons. 

    $ python -m jcvi.compara.catalog ortholog peach cacao --cscore=.99
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
![Grape-peach-cacao-fancy](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape-peach-cacao.png)

## Microsynteny visualization
What if we want to look at **local** synteny? Local synteny mostly focuses on gene-level, showing matching regions along with aligned gene models. For this, we'll need to compute the layout for the gene-level matchings:

    $ python -m jcvi.compara.synteny mcscan grape.bed grape.peach.lifted.anchors --iter=1 -o grape.peach.i1.blocks

Note that the option ``--iter=1`` says we are extracting *one* best region matching each grape region. If you take a look at the resulting ``grape.peach.i1.blocks`` file, it contains grape as the first column, and peach as the second column. If the option ``--iter`` is set to 2, there will be 2 peach regions, and so on. Specifically, this will be useful to plot regions resulted from genome duplications. 

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
![Grape-peach-blocks](https://dl.dropboxusercontent.com/u/15937715/Data/github/grape.peach.blocks.png)

It is also possible to do more than two matching regions! Similar to the macro-synteny plots, first construct multi-synteny blocks using ``python -m jcvi.compara.synteny mcscan``, then modify the ``blocks.layout`` file to indicate more regions as well as edges between the regions.