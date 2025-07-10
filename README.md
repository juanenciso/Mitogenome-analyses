# 🧬 Pipeline for Mitochondrial Genome Analysis
!Attention the following pipeline is still underconstruction! 


---

## Step 1: Assembly with Illumina seqs only
```
#BWA mem     do it in the Illumina folder
bwa index Scaphoideus_maai.fasta
bwa mem -t 12 /media/juan/data/ScaphoideusIllumina/Scaphoideus_maai.fasta ECH_1_1.fastq.gz ECH_1_2.fastq.gz| samtools sort -n -@4 -o mapping_mito.bam -
```

## Step 2: Count mapping reads
```
samtools view -c -F 0x904 mapping_mito.bam          #->244586
```
## Step 3: Select mapping reads
```
samtools view -b -F 0x904 mapping_mito.bam > mapping_mito_only.bam
```
## Step 4: Split mapped reads in R1 and R2:
```
samtools fastq -@ 8 mapping_mito_only.bam -1 mito_R1.fastq.gz -2 mito_R2.fastq.gz -0 /dev/null -s /dev/null -n
```
## Step 5: Assembly with Spades
```
/home/schuler/Software/SPAdes-3.15.4-Linux/bin/spades.py -1 mito_R1.fastq.gz -2 mito_R2.fastq.gz --meta -t 12 -o spades_mito1
```

## Step 6: Select mapping reads#info assembly (Contigs the first one) e.x NODE_1_length_14675_cov_1403.218536
```
blastn -task megablast -query contigs.fasta -db /media/schuler/data/Blast_DB/nt -evalue 1e-5 -num_threads 2 -max_target_seqs 1 -outfmt '6 qseqid sseqid stitle length pident evalue' -out OUT.megablast.fasta
```
