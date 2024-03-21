# Population comparison 2 - using bash first to make it more efficient

This starts by converting vcf files to tab files as in "https://github.com/TharinduTS/Comparing_populations"

also it uses a slightly altered python script from that github page (which is included at the end of this page)

# from tab files

First, find the column numbers of the individuals you want to compare with the following argument.
Run this in the directory with tab files
You can just edit samp_names and copy paste this on terminal
```
samp_name_1="F_Ghana_WZ_BJE4687_combined__sorted.bam"
samp_name_2="all_ROM19161_sorted.bam"
samp_name_3="all_calcaratus_sorted.bam"

mkdir ../"${samp_name_1}${samp_name_2}${samp_name_3}_selected_samples"

samp_1_col_no=$(head -1 combined_Chr10.g.vcf.gz_Chr10_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab | tr '\t' '\n' | cat -n | grep $samp_name_1 |cut -f 1)

samp_2_col_no=$(head -1 combined_Chr10.g.vcf.gz_Chr10_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab | tr '\t' '\n' | cat -n | grep $samp_name_2 |cut -f 1)

samp_3_col_no=$(head -1 combined_Chr10.g.vcf.gz_Chr10_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab | tr '\t' '\n' | cat -n | grep $samp_name_3 |cut -f 1)

echo $samp_1_col_no,$samp_2_col_no,$samp_3_col_no
```
# Include columns 1,2 and 3 as mandatory because they are needed info columns, and then add the numbers from previous command to the cut numbers and cut needed columns
```
for i in *vcf.gz.tab; do cut -f 1,2,3,4,21,22 $i > ../"${samp_name_1}${samp_name_2}${samp_name_3}_selected_samples"/${i}_selected_samples.tab;done
```
Then remove all ./. sites. Run this inside the directory with previously filtered tab files
```
mkdir no_empty
for i in *tab_selected_samples.tab; do grep -vwE "./." ${i} > no_empty/${i}_no_empty.tab;done

```

Copy this python script to the main directory for NOVOPlasty


```python
