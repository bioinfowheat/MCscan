Within the ALLMAPS run folder, we should be able to see a file with suffix `.chain`. Let's say our current annotations are in `scaffold.gff3`, together with the chain file `scaffold_to_genome.chain`, we can run this command:

```bash
$ liftOver -gff scaffold.gff3 scaffold_to_genome.chain genome.gff3 unmapped
```

The arguments are:
- `scaffold.gff3`: scaffold annotations (INPUT)
- `scaffold_to_genome.chain`: translations (INPUT)
- `genome.gff3`: genome annotations (OUTPUT)
- `unmapped`: things that fail (OUTPUT)

This command usually runs quickly. Whatever features fail in this operation are placed in the `unmapped` file, which should always be empty in the case of using ALLMAPS chain file. This command also works on `.bed` files without the `-gff` flag.

Download liftOver tool here if you can't locate it on your system:
http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/