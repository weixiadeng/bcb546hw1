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

1. The original data files are in `UNIX_Assignment` folder, intermediate files are in `tmp` folder, final output save in `output` folder.

2. Subset the maize from `feng` data to produce `maize.txt` to `tmp` folder.
    * Code: `grep ZMM fang_et_al_genotypes.txt > /home/wdeng/bcb546hw1/tmp/maize.txt`

3. Subset the header of `feng` data, save to tmp folder as `fang_head.txt`
    * Code: `head -1 fang_et_al_genotypes.txt > /home/wdeng/bcb546hw1/tmp/fang_head.txt`

4. Add the `fang` data header to the maize data, then delete its first three column `Sample_ID`, `JG_OTU`, and `Group`, save as `maize_head.txt`.
    * Code: `cat fang_head.txt maize.txt | cut -f1,2,3 --complement > maize_head.txt`

5. Transpose `maize_head.txt`, save as `trans_maize.txt`.
    * Code: `awk -f transpose.awk maize_head.txt > trans_maize.txt`

6. Split `snp` data to 10 txt files by `chromosome` 1 to 10, then only keep columns 1 for `SNP_ID`, 2 for `Chromosome`, and 4 for `nucleotide`, create `chromosome_1.txt` etc. at tmp folder.
    * Code: `for i in {1..10}; do awk '$3 == "'$i'"' snp_position.txt | cut -f1,3,4 > /home/wdeng/bcb546hw1/tmp/"chromosome_${i}.txt"; done`

7. Join 10 `chromosome_1.txt` etc with `trans_maize.txt` by `SNP_ID` (first column), save as `joined_maize_1.txt` etc.
    * Code: `for i in {1..10}; do join -1 1 -2 1 -t $'\t' chromosome_$i.txt trans_maize.txt > joined_maize_$i.txt; done`

8. Sort `joined_maize_1.txt` etc by increasing nucleotide position, output to `output` folder as `incre_joined_maize_1.txt` (total 10 files).
    * Code: `for i in {1..10}; do sort -k3,3n joined_maize_${i}.txt > /home/wdeng/bcb546hw1/output/incre_joined_maize_${i}.txt; done`

9. Sort `joined_maize_1.txt` etc by decreasing nucleotide position, then replace missing data `?` by symbol `-`, output to `output` folder as `decre_joined_maize_1.txt` (total 10 files).
    * Code: `for i in {1..10}; do sort -k3 -rn joined_maize_${i}.txt | sed 's/\?/\-/g' > /home/wdeng/bcb546hw1/output/decre_joined_maize_${i}.txt; done`

10. Subset `unknown` and `multiple` positions from `snp` data to `tmp` folder, then only keep columns 1 for `SNP_ID`, 2 for `Chromosome`, and 4 for `nucleotide`, save as `chromosome_unknown.txt` and `chromosome_multiple.txt` at `tmp` folder.
    * Code: `awk '$3 == "unknown"' snp_position.txt | cut -f1,3,4 > /home/wdeng/bcb546hw1/tmp/"chromosome_unknown.txt"`
    * Code: `awk '$3 == "multiple"' snp_position.txt |cut -f1,3,4 > /home/wdeng/bcb546hw1/tmp/"chromosome_multiple.txt"`

11. Join `chromosome_unknown.txt` and `chromosome_multiple.txt` with `trans_maize.txt` by `SNP_ID`, save as `joined_maize_unknown.txt` and `joined_maize_multiple.txt` to `output` folder.
    * Code: `join -1 1 -2 1 -t $'\t' chromosome_unknown.txt trans_maize.txt > /home/wdeng/bcb546hw1/output/"joined_maize_unknown.txt"`
    * Code: `join -1 1 -2 1 -t $'\t' chromosome_multiple.txt trans_maize.txt > /home/wdeng/bcb546hw1/output/"joined_maize_multiple.txt"`

### Teosinte Data

1. Subset the teosinte from `feng` data to produce `teosinte.txt` to `tmp` folder.
    * Code: `grep ZMP fang_et_al_genotypes.txt > /home/wdeng/bcb546hw1/tmp/teosinte.txt`

2. Add the `fang` data header to the teosinte data, then delete its first three column `Sample_ID`, `JG_OTU`, and `Group`, save as `teosinte_head.txt`.
    * Code: `cat fang_head.txt teosinte.txt | cut -f1,2,3 --complement > teosinte_head.txt`

3. Transpose `teosinte_head.txt`, save as `trans_teosinte.txt`.
    * Code: `awk -f transpose.awk teosinte_head.txt > trans_teosinte.txt`

4. Join 10 `chromosome_1.txt` etc with `trans_teosinte.txt` by `SNP_ID` (first column), save as `joined_teosinte_1.txt` etc.
    * Code: `for i in {1..10}; do join -1 1 -2 1 -t $'\t' chromosome_$i.txt trans_teosinte.txt > joined_teosinte_$i.txt; done`

5. Sort `joined_teosinte_1.txt` etc by increasing nucleotide position, output to `output` folder as `incre_joined_teosinte_1.txt` (total 10 files).
    * Code: `for i in {1..10}; do sort -k3,3n joined_teosinte_${i}.txt > /home/wdeng/bcb546hw1/output/incre_joined_teosinte_${i}.txt; done`

6. Sort `joined_teosinte_1.txt` etc by decreasing nucleotide position, then replace missing data `?` by symbol `-`, output to `output` folder as `decre_joined_teosinte_1.txt` (total 10 files).
    * Code: `for i in {1..10}; do sort -k3 -rn joined_teosinte_${i}.txt | sed 's/\?/\-/g' > /home/wdeng/bcb546hw1/output/decre_joined_teosinte_${i}.txt; done`

7. Join `chromosome_unknown.txt` and `chromosome_multiple.txt` with `trans_teosinte.txt` by `SNP_ID`, save as `joined_teosinte_unknown.txt` and `joined_teosinte_multiple.txt` to `output` folder.
    * Code: `join -1 1 -2 1 -t $'\t' chromosome_unknown.txt trans_teosinte.txt > /home/wdeng/bcb546hw1/output/"joined_teosinte_unknown.txt"`
    * Code: `join -1 1 -2 1 -t $'\t' chromosome_multiple.txt trans_teosinte.txt > /home/wdeng/bcb546hw1/output/"joined_teosinte_multiple.txt"`


