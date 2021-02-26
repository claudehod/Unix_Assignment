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

### `fang_et_al_genotypes` Maize Group

1. Grabbing ZMMIL, ZMMLR, and ZMMMR groups and pushing it into a new file.
  ```
  $ grep 'ZMMIL\|ZMMMR\|ZMMLR' fang_et_al_genotypes.txt > maize_genotypes.txt
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
 $ awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
  ```
