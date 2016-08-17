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


