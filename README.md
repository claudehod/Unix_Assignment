# **Unix_Assignment README**

### This document will describe my workflow for completing this assigmnet


## *Data Inspection*

### Attributes of `fang_et_al_genotypes`
- **Word Count:**
  ```
  $ wc fang_et_al_genotypes.txt
  ```
  The output of this command was:
  ```
  2783  2744038 11051939 fang_et_al_genotypes.txt
  ```
  Indicating that the line count was 2783, the word count was 2644038, and the character count was 11051939.

- **File Size:**

  ```
  $ du -h fang_et_al_genotypes.txt
  ```
  The output of this command was:
  ```
  6.1M	fang_et_al_genotypes.txt
  ```
  Indicating that the file size is 6.1 megabytes.

- **Number of Columns:**
  ```
  $ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
  ```
  The output of this command was:
  ```
  986
  ```
  Indicating that there are 986 columns in this file. This seemed like a lot, so I then double checked this information by using:
  ```
  $ tail -n 1 fang_et_al_genotypes.txt
  ```
  Which then gave me quite a lot of information. I did not want to count each individual output, so I will thus assume that there are indeed 986 columns in this file.


### Attributes of `snp_position`


- **Word Count:**
  ```
  $ wc snp_position.txt
  ```
  The output of this command was:
  ```
  984 13198 82763 snp_position.txt
  ```
  Indicating that the line count was 984, the word count was 13198, and the character count was 82763.

- **File Size:**

  ```
  $ du -h snp_position.txt
  ```
  The output of this command was:
  ```
  38K	snp_position.txt
  ```
  Indicating that the file size is 38 kilobytes.

- **Number of Columns:**
  ```
  $ awk -F "\t" '{print NF; exit}' snp_position.txt
  ```
  The output of this command was:
  ```
  15
  ```
  Indicating this file has 15 columns.




## *Data Processing*

### Maize Group

1. Grabbing ZMMIL, ZMMLR, and ZMMMR groups and pushing it into a new file.
  ```
  $ grep 'ZMMIL\|ZMMMR\|ZMMLR' fang_et_al_genotypes.txt > maize_genotypes.txt

  $ head -n 1 fang_et_al_genotypes.txt > fang_headers.txt

  $ cat fang_headers.txt maize_genotypes.txt > head_maize_genotype.txt
  ```
  Check the file to make sure only maize groups are pulled:
  ```
  $ cut -f 3 fang_et_al_genotypes.txt |sort| uniq -c

  ZMMIL 290
  ZMMLR 1256
  ZMMMR 27
  ```
2. Transpose the `maize_genotypes.txt` file.
  ```
  $ awk -f transpose.awk head_maize_genotype.txt > transposed_head_maize_genotype.txt
  ```
3. Sorting the files to prepare for joining.
  ```
  $ tail -n +2 snp_position.txt | sort -k1,1 > sorted_snp_position.txt

  $ tail -n +4 transposed_head_maize_genotype.txt | sort -k1,1 > sorted_transposed_head_maize_genotype.txt

  $ head -n 1 snp_position.txt > snp_position_headers.txt
  ```
4. Joining the files and adding the headers.
  ```
  $ join -t $'\t' -1 1 -2 1 sorted_snp_position.txt sorted_transposed_head_maize_genotype.txt > joined_maize_genotypes.txt

  cat snp_headers.txt joined_maize_genotypes.txt > joined_head_maize_genotypes.txt
  ```
5. Grabbing desired columns.
  ```
  $ cut -f 1,3,4,16- joined_head_maize_genotypes.txt > desired_columns_genotypes.txt

  $ head -n 1 desired_columns_genotypes.txt
    SNP_ID	Chromosome	Position
  ```
6. Forming chromosome files.
  ```
  $ awk '$2 == 1' desired_columns_genotypes.txt > chr1_maize_genotype.txt
  ```
  Repeat command for each of the 10 chromosomes:
  ```
  $ awk '$2 == 2' desired_columns_genotypes.txt > chr2_maize_genotype.txt
  $ awk '$2 == 3' desired_columns_genotypes.txt > chr3_maize_genotype.txt
  $ awk '$2 == 4' desired_columns_genotypes.txt > chr4_maize_genotype.txt
  $ awk '$2 == 5' desired_columns_genotypes.txt > chr5_maize_genotype.txt
  $ awk '$2 == 6' desired_columns_genotypes.txt > chr6_maize_genotype.txt
  $ awk '$2 == 7' desired_columns_genotypes.txt > chr7_maize_genotype.txt
  $ awk '$2 == 8' desired_columns_genotypes.txt > chr8_maize_genotype.txt
  $ awk '$2 == 9' desired_columns_genotypes.txt > chr9_maize_genotype.txt
  $ awk '$2 == 10' desired_columns_genotypes.txt > chr10_maize_genotype.txt
  ```
7. Sorting the chromosome files on increasing position of SNPs.
  ```
  sort -k3,3n chr1_maize_genotype.txt > sorted_chr1.txt
  ```
  Repeat for every chromosome file.

8. Sorting the chromosome files on decreasing position of SNPs.
  ```
  $ sort -k3,3nr chr1_maize_genotype.txt | sed 's/?/-/g' > reverse_chr1_maize_genotype.txt
  ```
9. Create "unknown" and "multiple" files.
  ```
  $ grep "unknown" desired_columns_genotypes.txt > unknown_maize.txt

  $ grep "multiple" desired_columns_genotypes.txt > multiple_maize.txt
  ```
10. Move all chromosome files to their own directory, so as not to drown in the 30 files created for chromosomes.
  ```
  $ mv chr*_maize_genotype.txt chromosome_genotype_files
  $ mv sorted_chr*.txt sorted_chromosome_files
  $ mv reverse*_genotype.txt reverse_sorted_chromosome_files
  ```

### Teosinte Group
Repeat data analysis for teosinte groups.

1. Grabbing ZMPBA, ZMPIL, and ZMPJA groups and pushing them into a new file.
  ```
  $ grep 'ZMPBA\|ZMPIL\|ZMPJA' fang_et_al_genotypes.txt > teosinte_genotypes.txt

  $ head -n 1 fang_et_al_genotypes > headers.txt

  $ cat headers.txt teosinte_genotypes.txt > headers_teosinte_genotypes.txt
  ```
2. Transpose the `teosinte_genotypes.txt` file.
  ```
  $ awk -f transpose.awk headers_teosinte_genotypes.txt > transposed_headers_teosinte_genotypes.txt
  ```
3. Sorting the files to prepare for joining.
  ```
  $ tail -n +2 snp_position.txt | sort -k1,1 > sorted_snp_position.txt
  ```
4. Joining the files and adding the headers.
  ```
  $ join -t $'\t' -1 1 -2 1 sorted_snp_position.txt sorted_transposed_headers_teosinte_genotypes.txt > joined_teosinte_genotypes.txt

  $ cat snp_position_headers.txt joined_teosinte_genotypes.txt > joined_head_teosinte_genotypes.txt
  ```
5. Grabbing desired columns.
  ```
  $ cut -f 1,3,4,16- joined_head_teosinte_genotypes.txt > desired_columns_genotypes.txt
  ```
6. Forming chromosome files.
  ```
  $ awk '$2 == 1' desired_columns_genotypes.txt > chr1_teosinte_genotype.txt
  ```
  Check to make sure you're doing it correctly.
  ```
  $ cut -f 2 chr1_teosinte_genotype.txt | sort -n | uniq -c
    155 1
  ```
  Repeat for each chromosome.

7. Sorting the chromosome files on increasing position of SNPs.
  ```
  $ sort -k3,3n chr1_teosinte_genotype.txt > sorted_chr1.txt
  ```
  Repeat for every chromosome file.

8. Sorting the chromosome files of decreasing position of SNPs.
  ```
  $ sort -k3,3nr chr1_teosinte_genotype.txt | sed 's/?/-/g' > reverse_chr1_teosinte.txt
  ```
  Repeat for each chromosome file.
9. Create the "unknown" and "multiple" files.
  ```
  $ grep "unknown" desired_columns_genotypes.txt > unknown_chromosomes.txt

  $ grep "multiple" desired_columns_genotypes.txt > multiple_chromosomes.txt
  ```
10. Move all chromosome files to their own directory for the same reason as last time.
  ```
  $ mv reverse*.txt reverse_sorted_chromosome_files/
  $ mv chr*_teosinte_genotype.txt chromosome_genotype_files
  $ mv sorted*.txt sorted_chromosome_files/
  ```
