# gbif.range

The *wsl.gbif* R library aims at easing the workflow of retrieving GBIF observations at large spatial scale for all species accepted names and synonyms, and filtering them according to the specific scale of a spatial analysis.

On the one hand, *wsl_gbif()* allows the whole observations of a given species name (accepted and synonym names) to be automatically retrieved, and improves the data accessibility of the *rgbif* R package (https://cran.r-project.org/web/packages/rgbif/index.html). The user download hard limit of *rgbif* is a maximum of 100,000 of species observations in one go if the easy-to-use interactive functions *occ_search()* and *occ_data()* are used (i.e., if no official download request is made with *occ_download()*, https://www.gbif.org/developer/occurrence). This impends the fast accessibility to the GBIF database when large observational datasets for many species and regions of the world are needed, specifically in scientific fields related to macroecology, modelling or satellite imagery. *wsl_gbif()* therefore bypasses this limit by intuitively using geographic parameters from *occ_data() in *rgbif* function and the *terra* R package and adopting a dynamic moving window process allowing the user's study area of interest to be automatically fragmented in several tiles that always include < 100,000 observations.

On the other hand, *wsl_gbif()* implements easy to use preliminary filtering options implemented during the download so that the user saves some post-processing time in data cleaning. 13 filters are available. Two already are set by default in *wsl_gbif()* (hasCoordinate = TRUE, hasGeospatialIssue=FALSE) and 11 can be chosen independently, including two that are based on the *CoordinateCleaner* R package (https://cran.r-project.org/web/packages/CoordinateCleaner/index.html). It is important to note that, although a strong records filtering may be undertaken with *wsl_gbif()*, *CoordinateCleaner* includes a larger variety of options that should be checked and applied on *wsl.gbif* outputs.

Finally, the *wsl.gbif* small library offers additional functions that post-process one or several *wsl_gbif()* outputs (e.g. taxonomy, doi, density filtering according to the study's resolution; see 'paper.md' for more details)

## Installation

You can install the development version from GitHub with:

``` r
remotes::install_github("8Ginette8/wsl.gbif")
```

## Example

Let's download worldwide the records of Panthera tigris only based on true observations:

``` r
# Download
obs.pt = wsl_gbif("Panthera tigris",basis=c("OBSERVATION","HUMAN_OBSERVATION"))

# Plot species records
library(maptools)
data(wrld_simpl)
plot(wrld_simpl)
points(obs.pt[,c("decimalLongitude","decimalLatitude")],pch=20,col="#238b4550",cex=4)
```

Note that an additional filtering needs here to be done as one observation is found in the US. A lot of tigers are being captive in this country hence the recorded observation. Therefore *CoordinateCleaner* functions should here be considered thereafter.

We can also retrieve all the tiger scientific names (accepted and synonyms) that were used in the download with the GBIF backbone taxonomy. If all = TRUE, additonal children and related doubtful names may also be extracted (not used in *wsl_gbif()*):

``` r
wsl_taxonomy("Panthera tigris",all=FALSE)
```

Same may be done with Delphinus delphis (a species with > 100'00 observations)

``` r
obs.dd = wsl_gbif("Delphinus delphis")
wsl_taxonomy("Delphinus delphis",all=TRUE) # Here the list is longer because 'all=TRUE' includes every names (even doubtful)
```

## Citation

Yohann Chauvier; Patrice Descombes; Michael P. Nobis; Katalin Csillery (2022). wsl.gbif - A R package to extract and compile GBIF datasets for large-scale analyses.  EnviDat.  doi: <a href="https://doi.org/10.16904/envidat.352">10.16904/envidat.352</a>

## References

Chauvier, Y., Thuiller, W., Brun, P., Lavergne, S., Descombes, P., Karger, D. N., ... & Zimmermann, N. E. (2021). Influence of climate, soil, and land cover on plant species distribution in the European Alps. Ecological monographs, 91(2), e01433. doi: <a href="https://doi.org/10.1002/ecm.1433">10.1002/ecm.1433</a>

Chamberlain, S., Oldoni, D., & Waller, J. (2022). rgbif: interface to the global biodiversity information facility API. doi: <a href="https://doi.org/10.5281/zenodo.6023735">10.5281/zenodo.6023735</a>

Zizka, A., Silvestro, D., Andermann, T., Azevedo, J., Duarte Ritter, C., Edler, D., ... & Antonelli, A. (2019). CoordinateCleaner: Standardized cleaning of occurrence records from biological collection databases. Methods in Ecology and Evolution, 10(5), 744-751. doi: <a href="https://doi.org/10.1111/2041-210X.13152">10.1111/2041-210X.13152</a>

Hijmans, Robert J. "terra: Spatial Data Analysis. R Package Version 1.6-7." (2022). Link to package: <a href="https://cran.r-project.org/web/packages/terra/index.html">Terra - CRAN</a>
