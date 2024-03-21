# pop_comp_grenedalf

```
bcftools view -s F_Ghana_WZ_BJE4687_combined__sorted.bam,F_IvoryCoast_xen228_combined__sorted.bam,F_Nigeria_EUA0331_combined__sorted.bam combined_Chr10.g.vcf.gz > test_filtered.vcf
```

```
gzip test_filtered.vcf
module load StdEnv/2020 vcftools/0.1.16
zcat test_filtered.vcf.gz | vcf-to-tab > out.tab
```
```
grep -vwE "./." out.tab > out_no_empty.vcf
```

```
grenedalf/bin/grenedalf frequency --vcf-path 2023_XT_combineGVCF_files/combined_Chr1.g.vcf.gz --write-sample-counts --allow-file-overwriting
```

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
for i in *vcf.gz.tab; do cut -f 1,2,3,4,21,22 $i ../"${samp_name_1}${samp_name_2}${samp_name_3}_selected_samples"/${i}_selected_samples.tab;done
```
# Then remove all ./. sites. Run this inside the directory with previously filtered tab files
```
mkdir no_empty
for i in *tab_selected_samples.tab; do grep -vwE "./." ${i} > no_empty/${i}_no_empty.tab;done

```
