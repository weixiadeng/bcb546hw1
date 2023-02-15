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

2. Subset the maize from `feng` data to produce `maize.txt` to `tmp` folder.
    * Code: `grep ZMM fang_et_al_genotypes.txt > /home/wdeng/HW1/tmp/maize.txt`

3. Subset the header of `feng` data, save to tmp folder as `fang_head.txt`
    * Code: `head -1 fang_et_al_genotypes.txt > /home/wdeng/HW1/tmp/fang_head.txt`

4. Add the `fang` data header to the maize data, save as `maize_head.txt`.
    * Code: `cat fang_head.txt maize.txt > maize_head.txt`

3. Transpose `maize_head.txt`, then sort its transpose by `SNP_ID`, save as `trans_sort_maize.txt`.
    * Code: `awk -f transpose.awk maize_head.txt | sort -k1,1V > trans_sort_maize.txt`

4. Split `snp` data to 10 txt files by `Chromosome` 1 to 10, create `Chromosome_1.txt` etc. at tmp folder.
    * Code: `for i in {1..10}; do awk '$3 == "'$i'"' snp_position.txt > /home/wdeng/HW1/tmp/"chromosome_${i}.txt"; done`  

5. Sort 10 `chromosome_1.txt` etc by `SNP_ID`, save as `sort_snp_chromosome_1.txt` etc.
    * Code: `for i in {1..10}; do sort -k1,1V chromosome_${i}.txt > /home/wdeng/HW1/tmp/"sort_snp_chromosome_${i}.txt"; done`

6. Join 10 `sort_snp_chromosome_1.txt` etc with `trans_sort_maize.txt` by `SNP_ID`, save as `joined_maize_1.txt` etc.
    * Code: `for i in {1..10}; do join -1 1 -2 1 -t $'\t' sort_snp_chromosome_$i.txt trans_sort_maize.txt > /home/wdeng/HW1/tmp/"joined_maize_${i}.txt"; done`

7. Sort `joined_maize_1.txt` etc by increasing nucleotide position, output to `tmp` folder as `incre_joined_maize_1.txt` (total 10 files).
    * Code: `for i in {1..10}; do sort -k4,4n joined_maize_${i}.txt > /home/wdeng/HW1/tmp/"incre_joined_maize_${i}.txt"; done`

8. Sort `joined_maize_1.txt` etc by decreasing nucleotide position, then replace missing data `?` by symbol `-`, output to `tmp` folder as `decre_joined_maize_1.txt` (total 10 files).
    * Code: `for i in {1..10}; do sort -k4 -rn joined_maize_${i}.txt | sed 's/\?/\-/g' > /home/wdeng/HW1/tmp/"decre_joined_maize_${i}.txt"; done`

9. Subset `unknown` positions from `snp` data to `tmp` folder, save as `chromosome_unknown.txt`.
    * Code: `awk '$3 == "unknown"' snp_position.txt > /home/wdeng/HW1/tmp/"chromosome_unknown.txt"`

10. Sort the `chromosome_unknown.txt` etc by `SNP_ID`, save as `sort_snp_chromosome_unknown.txt` etc.
    * Code: `sort -k1,1V chromosome_unknown.txt > /home/wdeng/HW1/tmp/"sort_snp_chromosome_unknown.txt"`

11. Subset `multiple` positions from `snp` data to `tmp` folder.
    * Code: `awk '$3 == "multiple"' snp_position.txt > /home/wdeng/HW1/tmp/"chromosome_multiple.txt"`

12. Sort the `chromosome_multiple.txt` etc by `SNP_ID`, save as `sort_snp_chromosome_multiple.txt` etc.
    * Code: `sort -k1,1V chromosome_multiple.txt > /home/wdeng/HW1/tmp/"sort_snp_chromosome_multiple.txt"`

13. Join `sort_snp_chromosome_unknown.txt` and `sort_snp_chromosome_multiple.txt` with `trans_sort_maize.txt` by `SNP_ID`, save as `joined_maize_unknown.txt` and `joined_maize_multiple.txt`
    * Code: `join -1 1 -2 1 -t $'\t' sort_snp_chromosome_unknown.txt trans_sort_maize.txt > /home/wdeng/HW1/tmp/"joined_maize_unknown.txt"`
    * Code: `join -1 1 -2 1 -t $'\t' sort_snp_chromosome_unknown.txt trans_sort_maize.txt > /home/wdeng/HW1/tmp/"joined_maize_multiple.txt"`


### Teosinte Data

1. Subset the teosinte from `feng` data to produce `teosinte.txt` to `tmp` folder.
    * Code: `grep ZMP fang_et_al_genotypes.txt > /home/wdeng/HW1/tmp/teosinte.txt`

2. Tranpose `teosinte.txt` to `trans_teosinte.txt`.
    * Code: `awk -f transpose.awk teosinte.txt > trans_teosinte.txt`



