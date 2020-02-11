We have included a number of visualization routines in the `jcvi` package that might be useful to create customized plots.

## Chromosome painting

Sometimes we wish to show categories for a number of chromosome regions in a genome. Let's say we want to plot the Arabidopsis genome. First, let's make a file that contains the chromosome sizes:

```
Chr1	30427671
Chr2	19698289
Chr3	23459830
Chr4	18585056
Chr5	26975502
```

This file can be created from the genome FASTA file and save it in `athaliana.sizes`. Now let's add a number of interesting features:

```
Chr1	1000000	3000000	type A
Chr4	12000000	15000000	type A
Chr2	5000000	10000000	type B
Chr5	20000000	22000000	type B
Chr2	15000000	18000000	LINE/SINE
Chr5	13000000	15000000	LINE/SINE
Chr3	2000000	3500000	LTR
```

This is known as the `bed` format, which must be __tab-separated__. Note that we use the functional class as the feature name. Since there are 4 classes, there will be 4 colors, one for each color. The command we use is:

```console
python -m jcvi.graphics.chromosome features.bed \
    --size=athaliana.sizes --title="*Arabidopsis* genome features" --gauge
```

![features_nocent](https://www.dropbox.com/s/3hr4l29mptruq1r/features_nocent.png?raw=1)

What if we want to include centromeres? Append the following lines to `features.bed`:

```
Chr1	14511721	14538721	centromere
Chr2	3611838	3611883	centromere
Chr3	13589756	13589816	centromere
Chr4	3133663	3133674	centromere
Chr5	11194537	11194848	centromere
```

Run the above command again, now with features indicating where the centromeres are. We have the following output:

![features](https://www.dropbox.com/s/g4xw8n5funownh6/features.png?raw=1)