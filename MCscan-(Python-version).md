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


