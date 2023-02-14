---
title: BCB 546 UNIX Assignment
author: Weixia Deng
date: 02/17/2023
---

# UNIX Assignment

## Data Inspection

### Attributes of `fang_et_al_genotypes.txt`

1. Data has 986 columns.
    * Code: `awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt`

2. Data has 2783 rows including header.
    * Code: `wc -l fang_et_al_genotypes.txt`

3. The first row of data is header, and the first three columns are `Sample_ID`, `JG_OTU` and `Group`. Rest columns are each SNP reads.
    * Code: `head -1 fang_et_al_genotypes.txt`

4. File disk usage is 6.1M.
    * Code: `du -h fang_et_al_genotypes.txt`

5. Data contains 1573 maize.
    * Code: `awk '{ print $3 }' fang_et_al_genotypes.txt | grep -c "^ZMM"`

6. Data contains 975 teosinte.
    * Code: `awk '{ print $3 }' fang_et_al_genotypes.txt | grep -c "^ZMP"`

### Attributes of `snp_position.txt`

1. Data has 15 columns.
    * Code: `awk -F "\t" '{print NF; exit}' snp_position.txt`

2. Data has 984 rows including header.
    * Code: `wc -l snp_position.txt`

3. The first row of data is header.
    * Code: `head -1 snp_position.txt`

4. File disk usage is 38K.
    * Code: `du -h snp_position.txt`

## Data Processing

### Maize Data

1. Sort `snp` data by third column `Chromosome` (increasing) .
    * Code: `tail -n +2 snp_position.txt | sort -k3,3V`
    * Code: `tail -n +2 snp_position.txt | sort -k3,3V | awk {'print $3'} | uniq`

2. Subset the maize from `feng` data to produce `maize.txt`.
    * Code: `grep ZMM fang_et_al_genotypes.txt > maize.txt`

3. Split `snp` data to 10 txt files by `Chromosome` 1 to 10, creat `Chromosome_1.txt` etc. at output folder.
    * Code: `for i in {1..10}; do awk '$3 == "'$i'"' snp_position.txt > /home/wdeng/HW1/output/"chromosome_${i}.txt"; done`  

### Teosinte Data

1. Subset the teosinte from `feng` data to produce `teosinte.txt`.
    * Code: `grep ZMP fang_et_al_genotypes.txt > teosinte.txt`



