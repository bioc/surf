


<!-- README.md is generated from README.Rmd. Please edit that file -->

# SURF

<!-- badges: start -->

[![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
<!-- badges: end -->

The **S**tatistical **U**tility for **R**BP **F**unctions (SURF) is an
integrative analysis framework to identify alternative splicing (AS),
alternative transcription initiation (ATI), and alternative
polyadenylation (APA) events regulated by individual RBPs and elucidate
protein-RNA interactions governing these events. We used SURF to
analyzed 104 RBP data (K562 cells, available from
[ENCODE](https://www.encodeproject.org)). Check out the results from
this [shiny](http://www.statlab.wisc.edu/shiny/surf/) app\!

We have a detailed vignette
[here](https://github.com/fchen365/surf/blob/master/vignettes/surf.R).

*A detailed vignette is in preparation, which we expect to put out by
**Winter 2020**\!*

## Installation

<!-- You can install the released version of `surf` from [CRAN](https://CRAN.R-project.org) with: -->

<!-- ``` r -->

<!-- install.packages("surf") -->

<!-- ``` -->

You can install the development version of `surf` from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("fchen365/surf")
```

## What can you do with SURF?

SURF is versatile in handling ATR event-centric analysis. Provided the
data, here are four different things you could do with SURF.

|   | Data                |      Format       | Task                                  |
| :-: | ------------------- | :---------------: | ------------------------------------- |
| 1 | genome annotation   | any (gtf, gff, …) | parse ATR events                      |
| 2 | \+ RNA-seq          |  alignment (bam)  | detect differential ATR events        |
| 3 | \+ CLIP-seq         |  alignment (bam)  | detect functional association         |
| 4 | \+ external RNA-seq | summarized table  | differential transcriptional activity |

## SURF Pipeline

### — One task at one call

The four tasks of SURF pipeline should be streamlined. Once you have the
data in hand (see the following sub-section), each step can be performed
with a single function:

``` r
library(surf)

event <- parseEvent(anno_file)                              # task 1
drr <- drseq(event, rna_seq_sample)                         # task 2
far <- faseq(drr, clip_seq_sample)                          # task 3
dar <- daseq(far, getRankings(exprMat), ext_sample)         # task 4
```

Here, `anno_file`, `rna_seq_sample`, `clip_seq_sample`, and `ext_sample`
are data description, and `exprMat` is a table of extra transcriptome
quantification (e.g., TCGA, GTEx, …).

### — Tell `surf` about your data

Describing your data should be easy. Simply follow the example below.

For task 1, a file directory will do.

``` r
anno_file <- "gencode.v24.annotation.filtered.gtf"
```

For task 2, `surf` needs to know where the alignment files (`bam`) are
and the experimental `condition` for differential analysis (e.g., RBP
“knock-down” and “wild-type” control).

``` r
rna_seq_sample <- data.frame(
  row.names = c('sample1', 'sample2', 'sample3', 'sample4'),
  bam = paste0("rna-seq/bam/sample", 1:4, ".bam"),
  condition = c('knock-down', 'knock-down', 'wild-type', 'wild-type'),
  stringsAsFactors = F
) 
```

Similarly for task 3, `surf` needs to know where the alignment files
(`bam`) are and the experimental `condition` (e.g., “IP” and the input
control “SMI”).

``` r
rna_seq_sample <- data.frame(
  row.names = c('sample5', 'sample6', 'sample7'),
  bam = paste0('clip-seq/bam/', 5:7, '.bam'),
  condition = c('IP', 'IP', 'SMI'),
  stringsAsFactors = F
)
```

Finally, for task 4, `surf` assumes that you have transcriptome
quantification summarized in a table `exprMat`, whose rows correspond to
genomic features (e.g., genes, transcripts, …) and columns correspond to
samples. You can use any your favorite measure (e.g. TPM, RPKM, …).
Then, let `surf` know of the sample group (`condition`):

``` r
ext_sample <- data.frame(
  row.names = colnames(exprMat),
  condition = rep(c('TCGA', 'GTEx'), c(173, 337))
)
```

## Reference

Chen, F., Keleş, S. SURF: integrative analysis of a compendium of
RNA-seq and CLIP-seq datasets highlights complex governing of
alternative transcriptional regulation by RNA-binding proteins. *Genome
Biol* **21**, 139 (2020).
[doi:10.1186/s13059-020-02039-7](https://doi.org/10.1186/s13059-020-02039-7)
