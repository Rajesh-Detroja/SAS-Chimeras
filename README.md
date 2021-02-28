### Following bioinformatic protocol have been developed to identify human Sense-Antisense (SAS) chimeric sequences from RNA-Seq data

<br></br>

#### Step 1: Mapping RNA-Seq reads to the database of human genome + human transcriptome + human SAS chimeras

```bash
bowtie2 --local -p 25 -x SAS_chimera_db.fa -1 sample.R1.fastq -2 sample.R1.fastq | grep -v -e "chr" -e "HLA" -e "ENST" > sample.chimera.sam
```

Here,

**bowtie2 --local** = Mapping algorithm used

**-p 25** = Number of CPUs used

**-x SAS_chimera_db.fa** = Bowtie2 index of merged database of human genome + human transcriptome + human SAS chimeras

**-1 input.R1.fastq** = forward sequencing reads

**-2 input.R2.fastq** = reverse sequencing reads


Finally, **Step 1** will generate a SAM file containing reads mapped to the human Sense-Antisense (SAS) chimeric sequences.

<br></br>

#### Step 2: Remove low-quality mapped reads and convert SAM file to BAM file

```bash
sambamba view -t 25 -S -F "not unmapped and mapping_quality >= 10" -f bam -o sample.chimera.bam sample.chimera.sam
```

Here,

**sambamba view** tool were used to remove low-quality mapped reads with **MAPQ < 10** and to convert **SAM** to **BAM** format of high-quality mapped reads

<br></br>

#### Step 3: Sort BAM file with high-quality mapped reads to human Sense-Antisense (SAS) chimeric sequences

```bash
sambamba sort --tmpdir=. -t 25 -o sample.chimera.sorted.bam sample.chimera.bam
```

Here,

**sambamba sort** tool were used to sort BAM file.


<br></br>

#### Step 4: Index sorted BAM file

```bash
sambamba index -t 25 sample.chimera.sorted.bam
```

Here,

**sambamba index** tool were used to index sorted BAM file.


