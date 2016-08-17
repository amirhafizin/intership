#!/usr/bin/bash

#make dir
mkdir -p /export/home/amirh/genome_dataset/mutation_scanning/process/run
# make the directory become main directory
cd /export/home/amirh/genome_dataset/mutation_scanning/process/run
echo making the new directory

#soflink data
#(/export/home/amirh/genome_dataset/mutation_scanning/input/fastq/3_1.fastq) location of data and name new date 1-1.fastq
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/input/fastq/3_1.fastq 1_1.fastq
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/input/fastq/3_2.fastq 1_2.fastq
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/input/index/Triticum_aestivum.IWGSC1+popseq.31.dna.genome.fa q.fa
echo making the soflink

#check if tools

#check for index file 
#if found skip if not create
if [ -e "/export/home/amirh/genome_dataset/mutation_scanning/input/index/q.idx" ];
then
echo skip
else
echo index the reference
# making the index file of the reference file 
#(/export/latestBin/novoindex) location of the programe 
# basic structure novoindex input output
/export/latestBin/novoindex /export/home/amirh/genome_dataset/mutation_scanning/output/q.idx q.fa
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/input/index/q.idx i.idx
fi 

#run align with novoalign
echo align with novoalign
/export/latestBin/novoalign -c 5 -t 60 -d /export/home/amirh/genome_dataset/mutation_scanning/input/index/q.idx -f 1_1.fastq 1_2.fastq  -o SAM 2> /export/home/amirh/genome_dataset/mutation_scanning/log/novoalign.stderr > /export/home/amirh/genome_dataset/mutation_scanning/output/align.sam
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/output/align.sam a.sam

#run conversion
echo running the sam to bam
/export/latestBin/samtools view -bS a.sam > /export/home/amirh/genome_dataset/mutation_scanning/output/finish.bam 
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/output/finish.bam f.bam

#mark the duplicate
echo mark the duplication
/export/latestBin/novosort -c 5 --markDuplicates --keepTags f.bam  2> /export/home/amirh/genome_dataset/mutation_scanning/log/novosort.stderr > /export/home/amirh/genome_dataset/mutation_scanning/output/mark.bam
ln -sf /export/home/amirh/genome_dataset/mutation_scanning/output/mark.bam m.bam

#run bam sorting index 
echo index
/export/latestBin/novosort -i -c 5 -s  m.bam --output /export/home/amirh/genome_dataset/mutation_scanning/output/index.bam 2> /export/home/amirh/genome_dataset/mutation_scanning/log/novosortindex.stderr


#check for index file 
#if found skip if not create
if [ -e "/export/home/amirh/genome_dataset/mutation_scanning/process/run/q.fa.fai" ];
then
echo skip
else
echo index the reference
# making the index file of the reference file 
/export/latestBin/samtools faidx q.fa
fi
 
#run the mpileup
echo run the mpileup
/export/latestBin/samtools mpileup -u -t DP -q 20 -A -f q.fa r.bam | /export/latestBin/bcftools call -vc -o /export/home/amirh/genome_dataset/mutation_scanning/output/raw.vcf | /export/latestBin/bcftools view /export/home/amirh/genome_dataset/mutation_scanning/output/raw.vcf
