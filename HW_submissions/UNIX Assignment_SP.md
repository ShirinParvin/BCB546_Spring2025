# **UNIX Assignment** by _Shirin Parvin_

### **Data Inspection** 

#### Attributes of `fang_et_al_genotypes.txt`

Code used for data inspection of the file:

```
$ du -h fang_et_al_genotypes.txt
$ wc -l fang_et_al_genotypes.txt
$ awk -F "\t" '{print NF;exit}' fang_et_al_genotypes.txt
$ less fang_et_al_genotypes.txt
```

By inspection this file, I found that:
1. `fang_et_al_genotypes` is a 6.7 MB text file consisting of 2783 lines and 986 columns
2. It has the paired base pair information for each SNP position.
3. Some of the columns have **?/?** indicating we do not have data for those positions

#### Attributes of `snp_position.txt`

Code used for data inspection of the file:

```
$ du -h snp_position.txt
$ wc -l snp_position.txt
$ awk -F "\t" '{print NF; exit}' snp_position.txt
$ less snp_position.txt
```
By inspection this file, I found that:
1. `snp_position.txt` is a 49 KB text file, consisting of 984 lines and 15 columns
2. It has information such as chromosome number, position, gene counts, amplicon counts, etc. 
3. A lot of the column names from `fang_et_al_genotypes` are rows in `snp_position`


### **Data Processing** 

###Maize Data
```
#Creating a separate file containing Maize genotype info.

$ grep -E 'Sample_ID|ZMMIL|ZMMLR|ZMMMR' fang_et_al_genotypes.txt > maize_genotype.txt
$ less maize_genotype.txt
$ grep -c 'Sample_ID' maize_genotype.txt
$ grep -c 'ZMMIL' maize_genotype.txt
$ grep -c 'ZMMLR' maize_genotype.txt
$ grep -c 'ZMMMR' maize_genotype.txt
$ awk -f transpose.awk maize_genotype.txt > transposed_maize_genotype.txt
$ less transposed_maize_genotype.txt

#Trimming down the snp_position.txt file to take out only the relevant info and then sorting it.
$ cut -f 1,3,4 snp_position.txt > snp_position_cut.txt
$ sort -k1,1 snp_position_cut.txt > snp_position_cut_sorted.txt


#sorting the transposed maize file 
$ sort -k1,1 transposed_maize_genotype.txt > transposed_maize_sorted.txt


#Creating the master dataset for maize
$ join -a1 -a2 -t$'\t' snp_position_cut_sorted.txt transposed_maize_sorted.txt > joined_cut_maize.txt
$ awk -F "\t" '{print NF; exit}' joined_cut_maize.txt

#Sorting the master dataset for maize according to chromosome number
$ sort -k2,2n -k3,3n joined_cut_maize.txt > joined_cut_maize_sorted.txt

#Generating files for increasing position value and missing data encoded by '?' symbol
$  for i in {1..10}; do awk -v chr=$i '$2 == chr {print $0}' joined_cut_
maize_sorted.txt > maize_chr${i}.txt; done

#Generating files for increasing position value and missing data encoded by '-' symbol
$ sed 's/?/-/g' joined_cut_maize_sorted.txt > maize_missing_encoded.txt
$ for i in {1..10}; do awk -v chr=$i '$2 == chr {print $0}' maize_missing_encoded.txt > maize_chr${i}_encoded.txt; done

#Generating file with all SNPs with **unknown** position in genome:
$ awk '$3 == "unknown" {print $0}' joined_cut_maize_sorted.txt > maize_S
NP_unknown.txt

#Generating file with all SNPs with **multiple** position in genome:
$ awk '$3 == "multiple" {print $0}' joined_cut_maize_sorted.txt > maize_
SNP_multiple.txt

```
First we select out the data which belong to the Maize Genotype, belonging to Group ZMMIL, ZMMLR, ZMMMR and store it into a new file called `maize_genotype.txt`. 
Then just as a sanity check, we check the number of instances of each of those and found: ZMMIL = 290, ZMMLR = 1256, ZMMMR = 27. 


###Teosinte Data
```
 $ grep -E 'Sample_ID|ZMPBA|ZMPIL|ZMPJA' fang_et_al_genotypes.txt > teosinte_genotype.txt
 $ less teosinte_genotype.txt
 $ grep -c 'Sample_ID' teosinte_genotype.txt
 $ grep -c 'ZMPBA' teosinte_genotype.txt
 $ grep -c 'ZMPIL' teosinte_genotype.txt
 $ grep -c 'ZMPJA' teosinte_genotype.txt
 $ awk -f transpose.awk teosinte_genotype.txt > tr
ansposed_teosinte_genotype.txt
 $ less transposed_teosinte_genotype.txt
 
 #Trimming down the snp_position.txt file to take out only the relevant info and then sorting it.
$ cut -f 1,3,4 snp_position.txt > snp_position_cut.txt
$ sort -k1,1 snp_position_cut.txt > snp_position_cut_sorted.txt

#sorting the transposed teosinte file
 $ sort -k1,1 transposed_teosinte_genotype.txt > transposed_teosinte_sorted.txt


#Creating the master dataset for teosinte
$ join -a1 -a2 -t$'\t' snp_position_cut_sorted.txt transposed_teosinte_sorted.txt > joined_cut_teosinte.txt
$ awk -F "\t" '{print NF; exit}' joined_cut_teosinte.txt

#Sorting the master dataset for maize according to chromosome number
$ sort -k2,2n -k3,3n joined_cut_teosinte.txt > joined_cut_teosinte_sorted.txt

#Generating files for increasing position value and missing data encoded by '?' symbol
$ for i in {1..10}; do awk -v chr=$i '$2 == chr {print $0}' joined_cut_teosinte_sorted.txt > teosinte_chr${i}.txt; done

#Generating files for increasing position value and missing data encoded by '-' symbol
$ sed 's/?/-/g' joined_cut_teosinte_sorted.txt > teosinte_missing_encode
d.txt
$ for i in {1..10}; do awk -v chr=$i '$2 == chr {print $0}' teosinte_mis
sing_encoded.txt > teosinte_chr${i}_encoded.txt; done

#Generating file with all SNPs with **unknown** position in genome:
$ awk '$3 == "unknown" {print $0}' joined_cut_teosinte_sorted.txt > teosinte_SNP_unknown.txt

#Generating file with all SNPs with **multiple** position in genome:
$ awk '$3 == "multiple" {print $0}' joined_cut_teosinte_sorted.txt > teosinte_SNP_multiple.txt

```
Similar to the Maize data, for the Teosinte data too, we select the data for the Teosinte Genotype, belonging to Group ZMPBA, ZMPIL, ZMPJA and store it into a new file called `teosinte_genotype.txt`. We checked the instances of the different groups and found: ZMPBA = 900, ZMPIL = 41 and ZMPJA = 34.


