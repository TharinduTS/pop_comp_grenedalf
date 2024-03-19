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
