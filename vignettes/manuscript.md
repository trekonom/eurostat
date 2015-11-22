---
title: "eurostat R package"
author: Przemyslaw Biecek, Janne Huovari, Markus Kainu, Leo Lahti
date: "2015-11-22"
bibliography: 
- bibliography.bib
- references.bib
output: 
  md_document:
    variant: markdown_github
---
<!--
%\VignetteEngine{knitr::rmarkdown}
%\VignetteIndexEntry{eurostat Markdown Vignette}
%\usepackage[utf8]{inputenc}
-->








To be submitted to [R Journal](https://journal.r-project.org/) ?

## Introduction

Governmental institutions have started to release increasing amount of
their data resources for the public as open data in the recent
years. This is opening novel opportunities for research and citizen
science. Eurostat, for instance, provides a rich collection of
demographic and economic data through its open data portal. The portal
currently includes ... data sets on ... between years ...

Efficient tools to access and analyse such data collections can
greatly benefit reproducible research. When the data is available, the
analytical methodology spanning from raw data to the final publication
can also be made available following reproducible research principles
(Gandrud, 2013). Standardization and automatization of
common data analysis tasks via dedicated software packages can greatly
facilitate reproducibility and code sharing, making the research more
transparent and efficient.

Here, we introduce the eurostat R package that implements automated
tools for R to access [open data from
Eurostat](http://ec.europa.eu/eurostat/) in [R statistical programming
language](http://www.r-project.org) (R Core Team, 2013). The
package has been actively developed by several independent
contributors and based on the community feedback in Github, and its
first CRAN release was on.. We are now reporting the first mature
version of the package that has been improved and tested by multiple
users, and includes features like cache, handling dates, and using the
tidy data principles , with the help of
the tidyr R package (Wickham, 2015a).


The work is based on our earlier CRAN packages
[statfi](http://cran.r-project.org/web/packages/statfi/index.html) and
[smarterpoland](http://cran.r-project.org/web/packages/SmarterPoland/index.html). Compared to this earlier work, we have now implemented an expanded
set of tools specifically focusing on the eurostat data collection. The
[datamart](http://cran.r-project.org/web/packages/datamart/index.html)
and the
[quandl](http://cran.r-project.org/web/packages/Quandl/index.html) R
packages [ADD REFS!!!] provide also access to certain versions of
eurostat data. In contrast to these generic database packages, our
work is fully focused on the Eurostat open data portal and provides
specific functionality suited for this data collection. There is a
development version for R package
[reurostat](https://github.com/Tungurahua/reurostat) but it does not
seem to be actively maintained at the moment (last commits at
...). The eurostat R package takes advantage of the
following external R packages:
devtools (Wickham and Chang, 2015),
dplyr (Wickham and Francois, 2015),
knitr (Xie, 2015a; Xie, 2015b; Xie, 2014),
ggplot2 (Wickham, 2009),
mapproj (Brownrigg, Minka, and Bivand., 2015),
plotrix (J, 2006),
reshape2 (Wickham, 2007),
rmarkdown (Allaire, Cheng, Xie, et al., 2015),
stringi (Gagolewski and Tartanus, 2015),
testthat (Wickham, 2011),
tidyr (Wickham, 2015a).
The eurostat R package is part of the
[rOpenGov](http://ropengov.github.io) collection
(Leo Lahti
Juuso Parkkinen and Kainu, 2013), which provides reproducible research
tools for computational social science and digital humanities.





## Overview of the functionality

The package includes tools to search and retrieve specific data sets
from the Eurostat open data portal, converting identifiers in
human-readable formats, selecting, modifying and visualizing the
data. Detailed installation instructions and usage examples are
provided in the [package
vignette](https://github.com/rOpenGov/eurostat/vignette/eurostat_tutorial.Rmd), and the [introductory blog post](http://ropengov.github.io/r/2015/05/01/eurostat-package-examples).

The unified interface to data sets can make data analysis more
straightforward and transparent by providing a standardized and
automated way to access the data sets. Here we describe the
functionality of the current CRAN release version (1.2.1). To install
this, simply use


```r
install.packages("eurostat")
```

You can download the complete table of contents of the database with the function `get_eurostat_toc()`, or use `search_eurostat()` to make a more focused search over the table of contents. To retrieve data sets for 'disposable income', for instance, use:



```r
library(eurostat)
results <- search_eurostat("disposable income", type = "dataset")
kable(head(results))
```



|     |title                                                                                                               |code        |type    |last.update.of.data |last.table.structure.change |data.start |data.end |values |
|:----|:-------------------------------------------------------------------------------------------------------------------|:-----------|:-------|:-------------------|:---------------------------|:----------|:--------|:------|
|1975 |Share of housing cost in disposable income by level of activity limitation, sex and age                             |hlth_dhc050 |dataset |17.07.2015          |17.07.2015                  |2005       |2013     |NA     |
|3733 |Gini coefficient of equivalised disposable income (source: SILC)                                                    |ilc_di12    |dataset |30.10.2015          |16.02.2015                  |1995       |2014     |NA     |
|3734 |Gini coefficient of equivalised disposable income before social transfers (pensions included in social transfers)   |ilc_di12b   |dataset |30.10.2015          |16.02.2015                  |2003       |2014     |NA     |
|3735 |Gini coefficient of equivalised disposable income before social transfers (pensions excluded from social transfers) |ilc_di12c   |dataset |30.10.2015          |16.02.2015                  |2003       |2014     |NA     |

The data type to search for is also specified. The options include 'table', 'dataset' or 'folder', referring to different levels of hierarchy in data organization: a 'table' resides in 'dataset' that are stored in a 'folder'. You can focus the search on a selected type.

The first lines of the output are shown above. Use values from the `code` column to refer to a specific data set in the subsequent download commands. The dataset identifier codes can also be browsed at the Eurostat
database \url{http://ec.europa.eu/eurostat/data/database}, which gives
codes in the Data Navigation Tree after every dataset in parenthesis.

Download the selected data with `get_eurostat()`. To retrieve the data set with a particular identifier, use 


```r
dat <- get_eurostat(id, time_format = "num")
```

The original data is annual in this example, hence we have here
selected a numeric time variable as it is more convient for annual
time series than the default date format. To improve the
interpretability of the output, the eurostat variable identifiers
could be further replaced with human-readable labels based on
definitions from Eurostat dictionaries with the `label_eurostat()`
function. The data is provided in the standard data.frame format, and
all standard tools for data subsetting and reshaping can be
conveniently applied.

The downloaded data sets are stored in cache by default. This can help
to avoid repeated downloading of identical data and helps to speed up
the analysis. Another advantage is that by storing an exact copy of
the data on the hard disk, it is possible to reproduce the analysis
results afterwards even if the source database has been updated.

Data sets containing geographic information can be visualized on a
map. Most indicators are of country-year -type, although some
indicators have data also at lower level of regional breakdown. The
disposable income of private households at [NUTS2](http://en.wikipedia.org/wiki/Nomenclature_of_Territorial_Units_for_Statistics) level can be
visualized, for instance, as in Figure XXX [PLACE THE FIGURE FROM MARKUS
BLOG POST WITH HIDDEN SOURCE CODE HERE]. For a more detailed treatment
of this example, see our related blog post [ADD LINK]. See more information on regional classification [here](http://ec.europa.eu/eurostat/ramon/nomenclatures/index.cfm?TargetUrl=DSP_GEN_DESC_VIEW_NOHDR&StrNom=NUTS_33&StrLanguageCode=EN).

Here, we have combined the data retrieved with the eurostat package with additional map visualization tools and utilities including
grid (R Core Team, 2015),
maptools (Bivand and Lewin-Koh, 2015),
rgdal (Bivand, Keitt, and Rowlingson, 2015),
rgeos (Bivand and Rundel, 2015), 
scales (Wickham, 2015b), and
stringr (Wickham, 2015c).

Example on spatio-temporal data visualization: let us look at the indicator [`tgs00026`](http://ec.europa.eu/eurostat/en/web/products-datasets/-/TGS00026), (*Disposable income of private households by NUTS 2 regions*) from Eurostat. We are looking at the disposable household income. In addition to downloading and manipulating data from EUROSTAT, we demonstrate how to access and use shapefiles of Europe published by EUROSTAT at [Administrative units / Statistical units](http://ec.europa.eu/eurostat/web/gisco/geodata/reference-data/administrative-units-statistical-units).


For the map example, look at Figure .


```
## [[1]]
## [1] TRUE
## 
## [[2]]
## [1] TRUE
## 
## [[3]]
## [1] TRUE
## 
## [[4]]
## [1] TRUE
## 
## [[5]]
## [1] TRUE
## 
## [[6]]
## [1] TRUE
```

```
## OGR data source with driver: ESRI Shapefile 
## Source: "./NUTS_2010_60M_SH/Data", layer: "NUTS_RG_60M_2010"
## with 1920 features
## It has 4 fields
```

![Figure 1: Eurostat spatial visualization example.](2015-manuscript-figures/2015-manu-RJournal_map_example-1.png) 


To make a triangle map from the plotrix (J, 2006)
package provides an example on visualizing passenger transport data
distributions (Figure YYY):


```
## [1] "Unit of measure"                                                                     
## [2] "Vehicles"                                                                            
## [3] "Geopolitical entity (reporting)"                                                     
## [4] "Period of time (a=annual, q=quarterly, m=monthly, d=daily, c=cumulated from January)"
## [5] NA
```

![plot of chunk 2015-manu-searchdata](2015-manuscript-figures/2015-manu-searchdata-1.png) ![plot of chunk 2015-manu-searchdata](2015-manuscript-figures/2015-manu-searchdata-2.png) 

To facilitate fast plotting of standard European geographic areas, the package provides ready-made lists of the country codes used in the eurostat database for EFTA (efta\_countries), Euro area (ea\_countries), EU (eu\_countries) and EU candidate countries (candidate\_countries). This helps to select specific groups of countries for closer investigation. For conversions with other standard country coding systems, see the countrycode R package (Arel-Bundock, 2014). To retrieve the eurostat country code list for EFTA, for instance, use:


```r
data(efta_countries)
kable(efta_countries)
```



|code |name          |
|:----|:-------------|
|IS   |Iceland       |
|LI   |Liechtenstein |
|NO   |Norway        |
|CH   |Switzerland   |

## Applications

The package or its predecessors have already been applied in several case studies by us and independent developers. Financial Times, for instance, [have used R to access data from Eurostat](http://blog.revolutionanalytics.com/2015/04/financial-times-tracks-unemployment-with-r.html) using functions from SmarterPoland, the basis for our revised and expanded eurostat package.

The [archivist](http://pbiecek.github.io/archivist) R package for archivisation of objects [has exemplified](http://pbiecek.github.io/archivist/justGetIT.html) its functionality by using eurostat to plot the number of people killed by road accidents, showing a decreasing trend of road accidents in many countries.

![Caption for this figure goes here.](2015-manuscript-figures/2015-manu-roadaccidents-1.png) 


We can also look at the distribution of BMI between different age groups:


```
##    time           bmi       sex           age            geo      
##  2008:7468   18P5-25:1900   F:2516   TOTAL  : 864   AT     : 432  
##              25-30  :1900   M:2412   Y18-24 : 864   BE     : 432  
##              GE30   :1896   T:2540   Y25-34 : 860   DE     : 432  
##              LT18P5 :1772            Y45-54 : 860   ES     : 432  
##                                      Y35-44 : 848   FR     : 432  
##                                      Y65-74 : 844   HU     : 432  
##                                      (Other):2328   (Other):4876  
##   isced97         values    
##  TOTAL:1867   Min.   : 0.1  
##  ED0.2:1867   1st Qu.: 7.9  
##  ED3_4:1867   Median :24.9  
##  ED5_6:1867   Mean   :26.4  
##               3rd Qu.:41.3  
##               Max.   :88.6  
##               NA's   :879
```

![plot of chunk 2015-manu-bmi](2015-manuscript-figures/2015-manu-bmi-1.png) 
 


## Discussion

The eurostat R package provides convenient tools to access open data
from Eurostat. When automated access to the data sets is integrated
with data analytical tools from other packages, this allows a seamless
automation of the data analytical process from raw data access to
statistical analysis and the final publication.

The package source code can be freely used, modified and distributed
under the BSD-2-clause (modified FreeBSD) license. A reproducible
version of this article is available at .. and can be used to generate
the manuscript text along with up-to-date figures and tables with the
latest version of the eurostat data. Manuscript automation provides
transparent documentation with full algorithmic details on how to
access, preprocess, analyse, and report data and analyses, thus
serving a template for good reproducible research practice. This article is written with a reproducible source code available at [eurostat github page](https://github.com/rOpenGov/eurostat/blob/master/vignettes/manuscript.Rmd).

The package exemplifies also the challenges and possible solutions to
reproducible research and automated open data retrieval. Possible
future extensions and improvements include design of specific data
representation class structures. This could facilitate harmonization
of the data representation with similar governmental data sets and
subsequent tool development. The latest development version of the
package can be installed from Github by following the instructions at
the [github site](https://github.com/rOpenGov/eurostat). We welcome
issues, bug reports and other feedback via [this development
site](https://github.com/ropengov/eurostat).





## Acknowledgements

We are grateful to [Eurostat](http://ec.europa.eu/eurostat/) for
maintaining the open data portal and the
[rOpenGov](https://github.ropengov.io) collection for supporting the
package development. This work has been partially funded by Academy of
Finland (decision ...). We also wish to thank Juuso Parkkinen and Joona Lehtomaki for their valuable feedback on this work.



## References



[1] J. Allaire, J. Cheng, Y. Xie, et al. _rmarkdown: Dynamic
Documents for R_. R package version 0.8.1. 2015. <URL:
http://rmarkdown.rstudio.com>.

[2] V. Arel-Bundock. _countrycode: Convert Country Names and
Country Codes_. R package version 0.18. 2014. <URL:
http://CRAN.R-project.org/package=countrycode>.

[3] R. Bivand, T. Keitt and B. Rowlingson. _rgdal: Bindings for
the Geospatial Data Abstraction Library_. R package version 1.0-7.
2015. <URL: http://CRAN.R-project.org/package=rgdal>.

[4] R. Bivand and N. Lewin-Koh. _maptools: Tools for Reading and
Handling Spatial Objects_. R package version 0.8-37. 2015. <URL:
http://CRAN.R-project.org/package=maptools>.

[5] R. Bivand and C. Rundel. _rgeos: Interface to Geometry Engine
- Open Source (GEOS)_. R package version 0.3-14. 2015. <URL:
http://CRAN.R-project.org/package=rgeos>.

[6] D. M. P. f. R. b. R. Brownrigg, T. P. Minka and t. t. P. 9. c.
b. R. Bivand. _mapproj: Map Projections_. R package version 1.2-4.
2015. <URL: http://CRAN.R-project.org/package=mapproj>.

[7] M. Gagolewski and B. Tartanus. _R package stringi: Character
string processing facilities_. 2015. DOI: 10.5281/zenodo.19071.
<URL: http://stringi.rexamine.com/>.

[8] C. Gandrud. _Reproducible Research with R and R Studio_.
Chapman \& Hall/CRC, Jul. 2013.

[9] L. J. "Plotrix: a package in the red light district of R". In:
_R-News_ 6.4 (2006), pp. 8-12.

[10] J. L. Leo Lahti Juuso Parkkinen and M. Kainu. _rOpenGov: open
source ecosystem for computational social sciences and digital
humanities_. Presentation at ICML/MLOSS workshop (Int'l Conf. on
Machine Learning - Open Source Software workshop). Dec. 2013.
<URL: http://ropengov.github.io>.

[11] R Core Team. _R: A language and environment for statistical
computing_. Vienna, Austria: R Foundation for Statistical
Computing, 2013. ISBN: ISBN 3-900051-07-0. <URL:
http://www.R-project.org/>.

[12] R Core Team. _R: A Language and Environment for Statistical
Computing_. R Foundation for Statistical Computing. Vienna,
Austria, 2015. <URL: https://www.R-project.org/>.

[13] H. Wickham. _ggplot2: elegant graphics for data analysis_.
Springer New York, 2009. ISBN: 978-0-387-98140-6. <URL:
http://had.co.nz/ggplot2/book>.

[14] H. Wickham. "Reshaping Data with the reshape Package". In:
_Journal of Statistical Software_ 21.12 (2007), pp. 1-20. <URL:
http://www.jstatsoft.org/v21/i12/>.

[15] H. Wickham. _scales: Scale Functions for Visualization_. R
package version 0.3.0. 2015. <URL:
http://CRAN.R-project.org/package=scales>.

[16] H. Wickham. _stringr: Simple, Consistent Wrappers for Common
String Operations_. R package version 1.0.0. 2015. <URL:
http://CRAN.R-project.org/package=stringr>.

[17] H. Wickham. "testthat: Get Started with Testing". In: _The R
Journal_ 3 (2011), pp. 5-10. <URL:
http://journal.r-project.org/archive/2011-1/RJournal_2011-1_Wickham.pdf>.

[18] H. Wickham. _tidyr: Easily Tidy Data with `spread()` and
`gather()` Functions_. R package version 0.3.1. 2015. <URL:
http://CRAN.R-project.org/package=tidyr>.

[19] H. Wickham and W. Chang. _devtools: Tools to Make Developing
R Packages Easier_. R package version 1.9.1. 2015. <URL:
http://CRAN.R-project.org/package=devtools>.

[20] H. Wickham and R. Francois. _dplyr: A Grammar of Data
Manipulation_. R package version 0.4.3. 2015. <URL:
http://CRAN.R-project.org/package=dplyr>.

[21] Y. Xie. _Dynamic Documents with R and knitr_. 2nd. ISBN
978-1498716963. Boca Raton, Florida: Chapman and Hall/CRC, 2015.
<URL: http://yihui.name/knitr/>.

[22] Y. Xie. "knitr: A Comprehensive Tool for Reproducible
Research in R". In: _Implementing Reproducible Computational
Research_. Ed. by V. Stodden, F. Leisch and R. D. Peng. ISBN
978-1466561595. Chapman and Hall/CRC, 2014. <URL:
http://www.crcpress.com/product/isbn/9781466561595>.

[23] Y. Xie. _knitr: A General-Purpose Package for Dynamic Report
Generation in R_. R package version 1.11. 2015. <URL:
http://yihui.name/knitr/>.

