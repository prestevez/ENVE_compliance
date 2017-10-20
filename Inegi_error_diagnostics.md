---
title: "Error diagnostics: ENVE Multi-year analysis"
author: "Patricio R. Estevez Soto"
email: "patricio.estevez.14@ucl.ac.uk"
date: "20/10/2017"
output:
    md_document:
    variant: "markdown"
pandoc_args: "--smart"
---

# Set up, data input and pre-process

## Session info

We first check details of the session and system, and for reproducibility, we set the random seed.


```r
starttime <- proc.time()
date()
```

```
## [1] "Fri Oct 20 16:44:58 2017"
```

```r
sessionInfo()
```

```
## R version 3.4.0 (2017-04-21)
## Platform: x86_64-pc-linux-gnu (64-bit)
## Running under: Red Hat Enterprise Linux Server 7.2 (Maipo)
## 
## Matrix products: default
## BLAS/LAPACK: /shared/ucl/apps/openblas/0.2.14/gnu-4.9.2/lib/libopenblasp-r0.2.14.so
## 
## locale:
##  [1] LC_CTYPE=en_GB.UTF-8       LC_NUMERIC=C              
##  [3] LC_TIME=en_GB.UTF-8        LC_COLLATE=en_GB.UTF-8    
##  [5] LC_MONETARY=en_GB.UTF-8    LC_MESSAGES=en_GB.UTF-8   
##  [7] LC_PAPER=en_GB.UTF-8       LC_NAME=C                 
##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
## [11] LC_MEASUREMENT=en_GB.UTF-8 LC_IDENTIFICATION=C       
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## loaded via a namespace (and not attached):
## [1] compiler_3.4.0 magrittr_1.5   tools_3.4.0    stringi_1.1.5 
## [5] knitr_1.16     stringr_1.2.0  evaluate_0.10
```

```r
set.seed(42)
options(scipen=0)
```

## Load packages and functions

Install custom package, requires `devtools`.


```r
# devtools::install_github("prestevez/victim")
```

Next we load the packages that we will use.


```r
# library(victim)
library(foreign)
# library(ggplot2)
# library(Cairo)
# library(knitr)
# library(texreg)
# library(lme4)
# library(glmmADMB)
# library(classInt)
# library(dplyr)
# library(reshape2)
# library(lmtest)
# library(car)
# library(pscl)
sessionInfo()
```

```
## R version 3.4.0 (2017-04-21)
## Platform: x86_64-pc-linux-gnu (64-bit)
## Running under: Red Hat Enterprise Linux Server 7.2 (Maipo)
## 
## Matrix products: default
## BLAS/LAPACK: /shared/ucl/apps/openblas/0.2.14/gnu-4.9.2/lib/libopenblasp-r0.2.14.so
## 
## locale:
##  [1] LC_CTYPE=en_GB.UTF-8       LC_NUMERIC=C              
##  [3] LC_TIME=en_GB.UTF-8        LC_COLLATE=en_GB.UTF-8    
##  [5] LC_MONETARY=en_GB.UTF-8    LC_MESSAGES=en_GB.UTF-8   
##  [7] LC_PAPER=en_GB.UTF-8       LC_NAME=C                 
##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
## [11] LC_MEASUREMENT=en_GB.UTF-8 LC_IDENTIFICATION=C       
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] foreign_0.8-67
## 
## loaded via a namespace (and not attached):
## [1] compiler_3.4.0 magrittr_1.5   tools_3.4.0    stringi_1.1.5 
## [5] knitr_1.16     stringr_1.2.0  evaluate_0.10
```



# Other diagnostics


```r
getwd()
```

```
[1] "/nfs/cfs/home2/uctz/uctzpre/projects/ENVE_compliance"
```

```r
list.files()
```

```
 [1] "cache"                            "enve2012cuest_ciega_2012.dbf"    
 [3] "enve2012delitos_ciega_2012.dbf"   "enve2014cuest_ciega_2014.dbf"    
 [5] "enve2014delitos_ciega_2014.dbf"   "enve2016cuest_ciega_2016.dbf"    
 [7] "enve2016delitos_ciega_2016.dbf"   "Inegi_error_diagnostics.md"      
 [9] "Inegi_error_diagnostics.Rmd"      "Multiple_Years_basic_EDA.html"   
[11] "Multiple_Years_basic_EDA.md"      "Multiple_Years_basic_EDA.Rmd"    
[13] "Multiple_Years_basic_WINDOWS.Rmd" "README.md"                       
[15] "testdata"                        
```

END
