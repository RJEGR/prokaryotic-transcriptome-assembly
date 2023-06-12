```bash
WD=/raid1/users/ricardo.gomez/

cd $WD/ASSEMBLY/

# inputs
cat $WD/genoma/*.fasta > ref.fa
seqkit stats $WD/genoma/*.fasta

# A) BOWTIE
# 1) build index

bowtie2-build ref.fa ref

# 2) 

#bowtie2 -x ref -1 *1.trimmed.fastq.gz -2 *2.trimmed.fastq.gz > SRR359063.bt2.sam

ln -s /raid1/users/ricardo.gomez/transcriptomas/*gz .

thread_count=8

for i in $(ls *.R1.fastq.gz);
do
withpath="${i}"
filename=${withpath##*/}
base="${filename%.R1.fastq.gz}"

left_file=${base}.R1.fastq.gz
right_file=${base}.R2.fastq.gz

bam_file=${base}.sorted.bam

bowtie2 -x ref -1 $left_file -2 $right_file -p $thread_count | samtools view -@ $thread_count -F 4 -S -b | samtools sort -@ $thread_count -n -o $bam_file

done

```

For genomes less than about 4 billion nucleotides in length, bowtie2-build builds a "small" index using 32-bit numbers in various parts of the index

```bash

# (IF) SEGEMEHL
# (fist install)

segemehl.x -d ref.fa -x ref.idx

segemehl.x -d ref.fa -i ref.idx -q $left_file -p $right_file -t $thread_count -S > ${base}.segemeh1.sam

```