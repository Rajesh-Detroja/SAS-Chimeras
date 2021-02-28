### Following bioinformatic protocol have been developed to identify human Sense-Antisense (SAS) chimeric sequences from RNA-Seq data

<br></br>

![](https://github.com/Rajesh-Detroja/SAS-Chimeras/blob/main/Workflow_Paper_1.png)

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

<br></br>

#### Step 5: Calculate SAS chimeras with mapped reads at junction region

```bash
bedtools coverage -f 1.00 -a SAS_chimera_db.bed -b sample.chimera.sorted.bam > sample.chimera.bed
```

here,

**bedtools coverage** tool were used to calculate SAS chimeras with mapped reads at junction region

**-a SAS_chimera_db.bed** Given junction regions of SAS chimeras


<br></br>

#### Step 6: Finally identify candidate SAS chimeras, If at-least 5 reads must be covered the given junction region of SAS chimeras

```bash
awk -v depth=5 '($7 >= 1 && $4 >= depth)' sample.chimera.bed | awk 'BEGIN { OFS="\t"; print "chimera", "start", "end", "depth", "covered_bp", "Junction_length", "coverage" } { print $0, "" }
```
