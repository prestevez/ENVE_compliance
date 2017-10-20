---
title: "Extortion in Mexico: Basic EDA of several sweeps of the ENVE"
author: "Patricio R. Estevez Soto"
email: "patricio.estevez.14@ucl.ac.uk"
date: "21/09/2017"
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
## [1] "Fri Oct 20 17:56:32 2017"
```

```r
sessionInfo()
```

```
## R version 3.3.1 (2016-06-21)
## Platform: x86_64-apple-darwin13.4.0 (64-bit)
## Running under: OS X 10.12.6 (Sierra)
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] foreign_0.8-67  devtools_1.12.0
## 
## loaded via a namespace (and not attached):
##  [1] magrittr_1.5     tools_3.3.1      withr_1.0.2      memoise_1.0.0   
##  [5] codetools_0.2-15 stringi_1.1.5    knitr_1.16       digest_0.6.12   
##  [9] stringr_1.2.0    evaluate_0.10
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
# check.packages function: install and load multiple R packages.
# Check to see if packages are installed. Install them if they are not, then load them into the R session.
check.packages <- function(pkg){
    new.pkg <- pkg[!(pkg %in% installed.packages()[, "Package"])]
    if (length(new.pkg))
        install.packages(new.pkg, dependencies = TRUE,
            repos = "https://cloud.r-project.org")
    sapply(pkg, require, character.only = TRUE)
}

check.packages("devtools")
```

```
## devtools 
##     TRUE
```

```r
install_version("foreign", version = "0.8-67",
    repos = "https://cloud.r-project.org")
```

```
## Downloading package from url: https://cloud.r-project.org/src/contrib/Archive/foreign/foreign_0.8-67.tar.gz
```

```
## Installing foreign
```

```
## '/Library/Frameworks/R.framework/Resources/bin/R' --no-site-file  \
##   --no-environ --no-save --no-restore --quiet CMD INSTALL  \
##   '/private/var/folders/zs/b92vw00n1ll1r4c_8kvrpn9w0000gn/T/RtmpFisQgj/devtoolsd9f14ba79529/foreign'  \
##   --library='/Users/prestevez/Library/R/3.3/library' --install-tests
```

```
## 
```

```
## Reloading installed foreign
```

```r
library(foreign)

sessionInfo()
```

```
## R version 3.3.1 (2016-06-21)
## Platform: x86_64-apple-darwin13.4.0 (64-bit)
## Running under: OS X 10.12.6 (Sierra)
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] foreign_0.8-67  devtools_1.12.0
## 
## loaded via a namespace (and not attached):
##  [1] httr_1.2.1       R6_2.2.2         magrittr_1.5     tools_3.3.1     
##  [5] withr_1.0.2      curl_2.3         memoise_1.0.0    codetools_0.2-15
##  [9] stringi_1.1.5    knitr_1.16       digest_0.6.12    stringr_1.2.0   
## [13] evaluate_0.10
```

```r
getwd()
```

```
## [1] "/Users/prestevez/R/ENVE_compliance"
```

```r
list.files()
```

```
##  [1] "cache"                                       
##  [2] "ENVE_compliance.Rproj"                       
##  [3] "enve2012cuest_ciega_2012.dbf"                
##  [4] "enve2012delitos_ciega_2012.dbf"              
##  [5] "enve2014cuest_ciega_2014.dbf"                
##  [6] "enve2014delitos_ciega_2014.dbf"              
##  [7] "enve2016cuest_ciega_2016.dbf"                
##  [8] "enve2016delitos_ciega_2016.dbf"              
##  [9] "Inegi_error_diagnostics.md"                  
## [10] "Inegi_error_diagnostics.Rmd"                 
## [11] "Multiple_Years_basic_EDA_Foreign_version.md" 
## [12] "Multiple_Years_basic_EDA_Foreign_version.Rmd"
## [13] "Multiple_Years_basic_EDA.html"               
## [14] "Multiple_Years_basic_EDA.md"                 
## [15] "Multiple_Years_basic_EDA.Rmd"                
## [16] "README.md"                                   
## [17] "testdata"
```




## Data description  

Se asume que los nombres de las tablas de datos son los siguientes:


- Enve 2012
    - Principal: `enve2012cuest_ciega_2012.dbf`
    - Delitos: `enve2012delitos_ciega_2012.dbf`
- Enve 2014
    - Principal: `enve2014cuest_ciega_2014.dbf`
    - Delitos: `enve2014delitos_ciega_2014.dbf`
- Enve 2016
    - Principal: `enve2016cuest_ciega_2016.dbf`
    - Delitos: `enve2016delitos_ciega_2016.dbf`

Si fueran diferentes se ruega se adapten los nombres en el bloque de codigo a continuacion.


```r
enve_2012 <- read.dbf("enve2012cuest_ciega_2012.dbf")
summary(enve_2012)
```

```
     P26_10        CVE_ENT     ID_ESTRATO      P33       SECTOR_FIN
 1      : 109   13     : 100   1:589      1      : 135   C:809     
 2      :  46   11     :  95   2:633      2      :  54   I:825     
 3      :  32   14     :  95   3:618      3      :  34   S:866     
 4      :  25   19     :  87   4:660      4      :  21             
 5      :  19   9      :  85              5      :  11             
 (Other):  66   21     :  84              (Other):  27             
 NA's   :2203   (Other):1954              NA's   :2218             
     P1_1B            P3         CVE_UNICA      P25_10   P29_1    P30_1   
 263010 :   4   2005   :  68   Min.   :    60   1: 297   1: 282   1: 282  
 383510 :   4   1982   :  66   1st Qu.: 10220   2:2153   2:2168   2:2168  
 390810 :   4   2008   :  66   Median : 19849   9:  50   9:  50   9:  50  
 419710 :   4   2013   :  66   Mean   : 32087                             
 589710 :   4   1991   :  65   3rd Qu.: 53126                             
 631010 :   4   2009   :  64   Max.   :100000                             
 (Other):2476   (Other):2105                                              
 P31_1    P32_1      ID_CONSECU    
 1: 282   1: 282   Min.   :    60  
 2:2168   2:2168   1st Qu.: 10220  
 9:  50   9:  50   Median : 19849  
                   Mean   : 32087  
                   3rd Qu.: 53126  
                   Max.   :100000  
                                   
```

```r
enve_delitos_2012 <- read.dbf("enve2012delitos_ciega_2012.dbf")
summary(enve_delitos_2012)
```

```
   ID_DELITO         M1_1       M1_3           M1_8        M1_11      
 02     :6679   12     : 4647   1: 7695   1      : 7145   1   :  411  
 10     :6655   11     : 4278   2:12273   2      : 3769   2   : 1317  
 03     :6443   06     : 3771   3:10540   3      : 1454   3   :  932  
 04     :5598   02     : 3272   4: 7924   9      :  573   4   :  690  
 05     :4839   03     : 3264   9: 1594   4      :  495   5   :10250  
 09     :2531   05     : 3204             (Other):  641   9   :  477  
 (Other):7281   (Other):17590             NA's   :25949   NA's:25949  
  M1_13       M1_14_1      M1_14_2      M1_14_3      M1_14_4     
 1   : 4832   1   : 2728   1   : 2182   1   :  204   1   :   76  
 2   : 6214   2   : 2068   2   : 2582   2   : 4562   2   : 4756  
 9   : 3031   9   :   36   9   :   68   9   :   66   NA's:35194  
 NA's:25949   NA's:35194   NA's:35194   NA's:35194               
                                                                 
                                                                 
                                                                 
  M1_15        M1_16       M1_18     M1_26       M5_1        M5_2_1     
 1   :  386   1   : 2347   1: 4595   1: 3588   1   : 6207   1   : 4824  
 2   : 4441   2   :11014   2:35367   2:36416   3   :   38   2   : 1814  
 9   :    5   9   :  330   9:   64   9:   22   4   :  207   9   :   17  
 NA's:35194   NA's:26335                       5   :   52   NA's:33371  
                                               6   :   31               
                                               7   :  120               
                                               NA's:33371               
  M5_2_2       M5_2_3       M5_2_4        M5_3         M5_4      
 1   :  185   1   :  243   1   : 1564   1   :  370   1   :   60  
 2   : 6393   2   : 6345   2   : 5091   2   : 4767   2   :  150  
 9   :   77   9   :   67   NA's:33371   9   :   29   9   :   49  
 NA's:33371   NA's:33371                NA's:34860   NA's:39767  
                                                                 
                                                                 
                                                                 
   CVE_UNICA       ID_CONSECU   
 Min.   :    1   Min.   :    1  
 1st Qu.: 5971   1st Qu.: 5971  
 Median :11664   Median :11664  
 Mean   :11647   Mean   :11647  
 3rd Qu.:17332   3rd Qu.:17332  
 Max.   :23390   Max.   :23390  
                                
```

```r
enve_2014 <- read.dbf("enve2014cuest_ciega_2014.dbf")
summary(enve_2014)
```

```
     P26_10        CVE_ENT     ID_ESTRATO      P33       SECTOR_FIN
 1      : 109   13     : 100   1:589      1      : 135   C:809     
 2      :  46   11     :  95   2:633      2      :  54   I:825     
 3      :  32   14     :  95   3:618      3      :  34   S:866     
 4      :  25   19     :  87   4:660      4      :  21             
 5      :  19   9      :  85              5      :  11             
 (Other):  66   21     :  84              (Other):  27             
 NA's   :2203   (Other):1954              NA's   :2218             
     P1_1B            P3         CVE_UNICA      P25_10   P29_1    P30_1   
 263010 :   4   2005   :  68   Min.   :    60   1: 297   1: 282   1: 282  
 383510 :   4   1982   :  66   1st Qu.: 10220   2:2153   2:2168   2:2168  
 390810 :   4   2008   :  66   Median : 19849   9:  50   9:  50   9:  50  
 419710 :   4   2013   :  66   Mean   : 32087                             
 589710 :   4   1991   :  65   3rd Qu.: 53126                             
 631010 :   4   2009   :  64   Max.   :100000                             
 (Other):2476   (Other):2105                                              
 P31_1    P32_1      ID_CONSECU    
 1: 282   1: 282   Min.   :    60  
 2:2168   2:2168   1st Qu.: 10220  
 9:  50   9:  50   Median : 19849  
                   Mean   : 32087  
                   3rd Qu.: 53126  
                   Max.   :100000  
                                   
```

```r
enve_delitos_2014 <- read.dbf("enve2014delitos_ciega_2014.dbf")
summary(enve_delitos_2014)
```

```
   ID_DELITO         M1_1       M1_3           M1_8        M1_11      
 02     :6679   12     : 4647   1: 7695   1      : 7145   1   :  411  
 10     :6655   11     : 4278   2:12273   2      : 3769   2   : 1317  
 03     :6443   06     : 3771   3:10540   3      : 1454   3   :  932  
 04     :5598   02     : 3272   4: 7924   9      :  573   4   :  690  
 05     :4839   03     : 3264   9: 1594   4      :  495   5   :10250  
 09     :2531   05     : 3204             (Other):  641   9   :  477  
 (Other):7281   (Other):17590             NA's   :25949   NA's:25949  
  M1_13       M1_14_1      M1_14_2      M1_14_3      M1_14_4     
 1   : 4832   1   : 2728   1   : 2182   1   :  204   1   :   76  
 2   : 6214   2   : 2068   2   : 2582   2   : 4562   2   : 4756  
 9   : 3031   9   :   36   9   :   68   9   :   66   NA's:35194  
 NA's:25949   NA's:35194   NA's:35194   NA's:35194               
                                                                 
                                                                 
                                                                 
  M1_15        M1_16       M1_18     M1_26       M5_1        M5_2_1     
 1   :  386   1   : 2347   1: 4595   1: 3588   1   : 6207   1   : 4824  
 2   : 4441   2   :11014   2:35367   2:36416   3   :   38   2   : 1814  
 9   :    5   9   :  330   9:   64   9:   22   4   :  207   9   :   17  
 NA's:35194   NA's:26335                       5   :   52   NA's:33371  
                                               6   :   31               
                                               7   :  120               
                                               NA's:33371               
  M5_2_2       M5_2_3       M5_2_4        M5_3         M5_4      
 1   :  185   1   :  243   1   : 1564   1   :  370   1   :   60  
 2   : 6393   2   : 6345   2   : 5091   2   : 4767   2   :  150  
 9   :   77   9   :   67   NA's:33371   9   :   29   9   :   49  
 NA's:33371   NA's:33371                NA's:34860   NA's:39767  
                                                                 
                                                                 
                                                                 
   CVE_UNICA       ID_CONSECU   
 Min.   :    1   Min.   :    1  
 1st Qu.: 5971   1st Qu.: 5971  
 Median :11664   Median :11664  
 Mean   :11647   Mean   :11647  
 3rd Qu.:17332   3rd Qu.:17332  
 Max.   :23390   Max.   :23390  
                                
```

```r
enve_2016 <- read.dbf("enve2016cuest_ciega_2016.dbf")
summary(enve_2016)
```

```
     P26_10        CVE_ENT     ID_ESTRATO      P33       SECTOR_FIN
 1      : 109   13     : 100   1:589      1      : 135   C:809     
 2      :  46   11     :  95   2:633      2      :  54   I:825     
 3      :  32   14     :  95   3:618      3      :  34   S:866     
 4      :  25   19     :  87   4:660      4      :  21             
 5      :  19   9      :  85              5      :  11             
 (Other):  66   21     :  84              (Other):  27             
 NA's   :2203   (Other):1954              NA's   :2218             
     P1_1B            P3         CVE_UNICA      P25_10   P29_1    P30_1   
 263010 :   4   2005   :  68   Min.   :    60   1: 297   1: 282   1: 282  
 383510 :   4   1982   :  66   1st Qu.: 10220   2:2153   2:2168   2:2168  
 390810 :   4   2008   :  66   Median : 19849   9:  50   9:  50   9:  50  
 419710 :   4   2013   :  66   Mean   : 32087                             
 589710 :   4   1991   :  65   3rd Qu.: 53126                             
 631010 :   4   2009   :  64   Max.   :100000                             
 (Other):2476   (Other):2105                                              
 P31_1    P32_1      ID_CONSECU    
 1: 282   1: 282   Min.   :    60  
 2:2168   2:2168   1st Qu.: 10220  
 9:  50   9:  50   Median : 19849  
                   Mean   : 32087  
                   3rd Qu.: 53126  
                   Max.   :100000  
                                   
```

```r
enve_delitos_2016 <- read.dbf("enve2016delitos_ciega_2016.dbf")
summary(enve_delitos_2016)
```

```
   ID_DELITO         M1_1       M1_3           M1_8        M1_11      
 02     :6679   12     : 4647   1: 7695   1      : 7145   1   :  411  
 10     :6655   11     : 4278   2:12273   2      : 3769   2   : 1317  
 03     :6443   06     : 3771   3:10540   3      : 1454   3   :  932  
 04     :5598   02     : 3272   4: 7924   9      :  573   4   :  690  
 05     :4839   03     : 3264   9: 1594   4      :  495   5   :10250  
 09     :2531   05     : 3204             (Other):  641   9   :  477  
 (Other):7281   (Other):17590             NA's   :25949   NA's:25949  
  M1_13       M1_14_1      M1_14_2      M1_14_3      M1_14_4     
 1   : 4832   1   : 2728   1   : 2182   1   :  204   1   :   76  
 2   : 6214   2   : 2068   2   : 2582   2   : 4562   2   : 4756  
 9   : 3031   9   :   36   9   :   68   9   :   66   NA's:35194  
 NA's:25949   NA's:35194   NA's:35194   NA's:35194               
                                                                 
                                                                 
                                                                 
  M1_15        M1_16       M1_18     M1_26       M5_1        M5_2_1     
 1   :  386   1   : 2347   1: 4595   1: 3588   1   : 6207   1   : 4824  
 2   : 4441   2   :11014   2:35367   2:36416   3   :   38   2   : 1814  
 9   :    5   9   :  330   9:   64   9:   22   4   :  207   9   :   17  
 NA's:35194   NA's:26335                       5   :   52   NA's:33371  
                                               6   :   31               
                                               7   :  120               
                                               NA's:33371               
  M5_2_2       M5_2_3       M5_2_4        M5_3         M5_4      
 1   :  185   1   :  243   1   : 1564   1   :  370   1   :   60  
 2   : 6393   2   : 6345   2   : 5091   2   : 4767   2   :  150  
 9   :   77   9   :   67   NA's:33371   9   :   29   9   :   49  
 NA's:33371   NA's:33371                NA's:34860   NA's:39767  
                                                                 
                                                                 
                                                                 
   CVE_UNICA       ID_CONSECU   
 Min.   :    1   Min.   :    1  
 1st Qu.: 5971   1st Qu.: 5971  
 Median :11664   Median :11664  
 Mean   :11647   Mean   :11647  
 3rd Qu.:17332   3rd Qu.:17332  
 Max.   :23390   Max.   :23390  
                                
```

END
