### Following bioinformatic protocols have been developed to identify human Sense-Antisense (SAS) chimeric sequences from RNA-Seq data


### Step 1

```bash
bowtie2 --local -p $threads -x $X -1 ${R1[i]} -2 ${R2[i]}
```


