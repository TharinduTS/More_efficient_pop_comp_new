# More_efficient_pop_comp_new

First I combined all the tab files into one file removing the header
```
tail -q -n +2 *selected.vcf.gz.tab > combined_chrs.tab
```
Then I added headers to the file
```
echo -e "#CHROM\tPOS\tREF\tF_Ghana_WZ_BJE4687_combined__sorted.bam\tF_IvoryCoast_xen228_combined__sorted.bam\tF_Nigeria_EUA0331_combined__sorted.bam\tF_Nigeria_EUA0333_combined__sorted.bam\tF_SierraLeone_AMNH17272_combined__sorted.bam\tF_SierraLeone_AMNH17274_combined__sorted.bam\tJBL052_concatscafs_sorted.bam\tM_Ghana_WY_BJE4362_combined__sorted.bam\tM_Ghana_ZY_BJE4360_combined__sorted.bam\tM_Nigeria_EUA0334_combined__sorted.bam\tM_Nigeria_EUA0335_combined__sorted.bam\tM_SierraLeone_AMNH17271_combined__sorted.bam\tM_SierraLeone_AMNH17273_combined__sorted.bam\tXT10_WZ_no_adapt._sorted.bam\tXT11_WW_trim_no_adapt_scafconcat_sorted.bam\tXT1_ZY_no_adapt._sorted.bam\tXT7_WY_no_adapt__sorted.bam\tall_ROM19161_sorted.bam\tall_calcaratus_sorted.bam\tmello_GermSeq_sorted.bam" | cat - combined_chrs.tab >/tmp/out && mv /tmp/out combined_chrs.tab
```

created a smaller test file to test my codes first

```
head -500 no_empty_all_chrs.tab>testing.tab
```
