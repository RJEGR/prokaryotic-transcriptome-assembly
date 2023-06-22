# User
Ricardo Gomez-Reyes
# System info
biobacter
```bash
cat /etc/*-release | grep "DISTRIB" # see linux distro and version

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.3 LTS"

free -h # check RAM memomy available

df -H 
```
# 0) Genome reference
```bash
WD=/raid1/users/ricardo.gomez/

seqkit stats $WD/genoma/*.fasta

# concat in a single file

cat $WD/genoma/*.fasta > ref.fa
```

# 1) RNA-seq alignment
## A) bowtie2
For genomes less than about 4 billion nucleotides in length, bowtie2-build builds a "small" index using 32-bit numbers in various parts of the index.
```bash
WD=/raid1/users/ricardo.gomez/

cd $WD/ASSEMBLY/

# A) BOWTIE
# 1) build index

bowtie2-build ref.fa ref

# 2) 
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

ls -ltrh *bam

```
## B) Segemehl:
The short read mapper Segemehl has been extensively used to assembly RNA-seq from prokaryote sampels (Ex: READemption pipeline from Förstner et al., 2014).

```bash


# B) SEGEMEHL
# (fist install)

export PATH=$PATH:"/opt/segemehl-0.3.4/"
 
segemehl.x -d ref.fa -x ref.idx

for i in $(ls *.R1.fastq.gz);
do
withpath="${i}"
filename=${withpath##*/}
base="${filename%.R1.fastq.gz}"

left_file=${base}.R1.fastq.gz
right_file=${base}.R2.fastq.gz

segemehl.x -d ref.fa -i ref.idx -q $left_file -p $right_file -t $thread_count -S > ${base}.segemeh1.sam
done


for i in $(ls *.segemeh1.sam);do samtools view $i -@ $thread_count -F 4 -S -b | samtools sort -@ $thread_count -n -o ${i%.sam}.sorted.bam; done



```
# 2) RNA-seq assembly
Both, bowtie2 and segemehl output  Sequence Alignment/Map format (SAM) files sets. The SAM alignment files needs to be converted into Binary Alignment/Map format (BAM) files to further analysis, including the assembly. For reference-based RNA-seq assemblers, annotation files including gene positions in GFF3/GTF format (Gene feature format) have to be provided. 
 
Because not gtf/gff file is provided:
```bash
# STRINGTIE

# git clone https://github.com/gpertea/stringtie
# cd stringtie
# make release

#

export PATH=$PATH:"/raid1/users/ricardo.gomez/ASSEMBLY/stringtie"

mkdir -p DENOVO_MODE;cd DENOVO_MODE

for i in $(ls *.sorted.bam)
do
withpath="${i}"
filename=${withpath##*/}
bs="${filename%*.sorted.bam}"
echo "stringtie --rf -p 12 -l $bs -o DENOVO_MODE/${bs}_transcripts.gtf $i"
done

#Error: the input alignment file is not sorted!
# Check dups

samtools view -H TvpW31.sorted.bam  | grep '^@SQ' | cut -f2 -d: | cut -f1 | sort | uniq -d

https://github.com/gpertea/stringtie/issues/240
```

FADU needs GTF file
```bash
export PATH=$PATH:"/opt/FADU-1.8.3"

# https://github.com/IGS/FADU

#/opt/TSSpredator-1.1beta.jar
```

# 3) Quality transcriptome assessment 
## 3.1) Output FASTA
```bash
..
```
## 3.2) BUSCO
```bash

# Revisar que otras db son utiles: 
# https://busco-archive.ezlab.org/frame_wget.html

eukaryota_odb9=/PATH_TO_BUSCO_DB/eukaryota_odb9
bacteria_odb9=/PATH_TO_BUSCO_DB/bacteria_odb9

run_BUSCO.py -i $fasta -l $eukaryota_odb9 -m transcriptome -o ${out}_eukaryota_odb9 -c 24

run_BUSCO.py -i $fasta -l $bacteria_odb9 -m transcriptome -o ${out}_bacteria_odb9 -c 24
```
