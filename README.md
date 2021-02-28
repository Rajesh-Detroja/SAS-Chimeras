### Following bioinformatic protocol have been developed to identify human Sense-Antisense (SAS) chimeric sequences from RNA-Seq data


#### Step 1: Mapping RNA-Seq reads to the database of human genome + human transcriptome + human SAS chimeras

```bash
bowtie2 --local -p 25 -x SAS_chimera_db.fa -1 input.R1.fastq -2 input.R1.fastq | grep -v -e "chr" -e "HLA" -e "ENST" > input.chimera.sam
```

Where, 

**bowtie2 --local** = Mapping algorithm used

**-p 25** = Number of CPUs used

**-x SAS_chimera_db.fa** = Bowtie2 index of merged database of human genome + human transcriptome + human SAS chimeras

**-1 input.R1.fastq** = forward sequencing reads

**-2 input.R2.fastq** = reverse sequencing reads


Finally, **Step 1** will generate a SAM file containing reads mapped to the human Sense-Antisense (SAS) chimeric sequences.

#### Step 2: Filter low-quality mapped reads and convert SAM file to BAM file

```bash
sambamba view -t 25 -S -F "not unmapped and mapping_quality >= 10" -f bam -o input.chimera.bam input.chimera.sam
```



