# gbif.range

The *gbif.range* R library contains automated methods to generate species range maps from scratch using ecoregions shapefiles and an easy-to-use GBIF wrapper. This library also offers a set of additional very useful functions meant to be employed on large GBIF datasets. The rationale of this library are the following:

(1) On the one hand, information on species distributions is easily retrievable online and usually based on expert range maps (e.g. IUCN polygons - https://www.iucnredlist.org/resources/spatial-data-download, EUFORGEN polygons - https://www.euforgen.org/species/). However, this availability remains limited as not all species have information on their expert distribution due to their rareness and the important number of taxa existing in the world. One common remedy is to employ models to infer and predict these distributions based on spatial environmental information and georeferenced records of taxa. Although very precise, such methods are unfortunately often very technical and need advanced statistical knowledge to be applied. This package therefore means to solve this 

## Description



On the other hand, the Global Biodiversity Information facility (GBIF) is a large public data repository inventorying georeferenced species observations from all taxonomic groups, with an increasing number of public, associative and private contributors that updates this database everyday by uploading their own datasets. The GBIF repository is freely accessible (https://www.gbif.org/), includes > two billions observations (data from 2022) and is frequently used by a vast community of ecologists, modellers, researchers and practitioners to address ecological, geological and environmental questions whose answers are becoming increasingly crucial in our current global change context. Since recently, retrieving GBIF species information may easily be done via new packages and libraries, e.g. rgbif on R or pygbif on python. However, the given accessibility of these libraries are impended by their technicalities, various functions and parameters, and limitations in the number of species observations a user may download at once when no official data request is made (< 100,000 records).

Therefore this package, first simplifies the extraction and compilation of large GBIF datasets by providing a user-friendly main search function *get_gbif* that automatically retrieve species records linked to several scientific names, bypasses the download hard limit of 100,000 observations of occ_data() in *rgbif*, and filter observations quality via several simple parameters. Second, by using the latter output, species range maps may be constructed with the *get_range* function which estimates species ranges based on occurrence data (GBIF or not) and bioregions shapefile using convex hull polygons.

On the one hand, *get_gbif()* allows the whole observations of a given species name (accepted and synonym names) to be automatically retrieved, and improves the data accessibility of the *rgbif* R package (https://cran.r-project.org/web/packages/rgbif/index.html). The user download hard limit of *rgbif* is a maximum of 100,000 of species observations in one go if the easy-to-use interactive functions *occ_search()* and *occ_data()* are used (i.e., if no official download request is made with *occ_download()*, https://www.gbif.org/developer/occurrence). This impends the fast accessibility to the GBIF database when large observational datasets for many species and regions of the world are needed, specifically in scientific fields related to macroecology, modelling or satellite imagery. *wsl_gbif()* therefore bypasses this limit by intuitively using geographic parameters from *occ_data() in *rgbif* function and the *terra* R package and adopting a dynamic moving window process allowing the user's study area of interest to be automatically fragmented in several tiles that always include < 100,000 observations.

On the other hand, *get_gbif()* implements easy to use preliminary filtering options implemented during the download so that the user saves some post-processing time in data cleaning. 13 filters are available. Two already are set by default in *get_gbif()* (hasCoordinate = TRUE, hasGeospatialIssue=FALSE) and 11 can be chosen independently, including two that are based on the *CoordinateCleaner* R package (https://cran.r-project.org/web/packages/CoordinateCleaner/index.html). It is important to note that, although a strong records filtering may be undertaken with *get_gbif()*, *CoordinateCleaner* includes a larger variety of options that should be checked and applied on *wsl.gbif* outputs.

Finally, *gbif.range* offers a set of additional very useful functions meant to be used for large-scale studies using GBIF observations:
  - *get_taxonomy*: Generates, based on a given species name, a list of all its scientific names
  (accepted, synonyms) found in the GBIF backbone taxonomy to download the data. Children and related
  doubtful names not used to download the data may also be extracted. The function allows therefore taxonomy
  correspondency to be made between different species and sub-species to potentially merge their records,
  but also permits efficient ways of linking external data of a species which is named differently across databases.
  - *obs_filter*: Whereas the 'grain' parameter in *get_gbif()* allows GBIF observations to be filtered
  according to a certain spatial precision, *obs_filter()* accepts as input a *get_gbif()* output (one or
  several species) and filter the observations according to a specific given grid resolution (one observation
  per pixel grid kept). This function allows the user to refine the density of GBIF observations according to
  a defined analysis/study's resolution.
  - *make_tiles*: May be used to generate a set of *SpatialExtent* and geometry arguments POLYGON() based on a given
  geographic extent. This function is meant to help users who want to use the *rgbif* R package and its parameter
  *geometry* that uses a POLYGON() argument.
  - *get_doi*: A small wrapper of *derived_dataset()* in *rgbif* that simplifies the obtention of a general DOI
  for a set of several gbif species datasets.

## Installation

You can install the development version from GitHub with:

``` r
remotes::install_github("8Ginette8/gbif.range")
```

## Example

Let's download worldwide the records of Panthera tigris only based on true observations:

``` r
# Download
obs.pt = get_gbif("Panthera tigris",basis=c("OBSERVATION","HUMAN_OBSERVATION"))

# Plot species records
library(maptools)
data(wrld_simpl)
plot(wrld_simpl)
points(obs.pt[,c("decimalLongitude","decimalLatitude")],pch=20,col="#238b4550",cex=4)
```

Note that an additional filtering needs here to be done as one observation is found in the US. A lot of tigers are being captive in this country hence the recorded observation. Therefore *CoordinateCleaner* functions should here be considered thereafter.

We can also retrieve all the tiger scientific names (accepted and synonyms) that were used in the download with the GBIF backbone taxonomy. If all = TRUE, additonal children and related doubtful names may also be extracted (not used in *wsl_gbif()*):

``` r
get_taxonomy("Panthera tigris",all=FALSE)
```

Same may be done with Delphinus delphis (a species with > 100'00 observations)

``` r
obs.dd = get_gbif("Delphinus delphis")
get_taxonomy("Delphinus delphis",all=TRUE) # Here the list is longer because 'all=TRUE' includes every names (even doubtful)
```

## Citation
Yohann Chauvier; Patrice Descombes; Oskar Hagen; Camille Albouy; Fabian Fopp; Michael P. Nobis; Philipp Brun; Lisha Lyu; Katalin Csilléry; Loïc Pellissier (2022). gbif.range - A R package to generate species range maps based on ecoregions and an user-friendly GBIF wrapper. EnviDat. doi: <a href="https://www.envidat.ch/#/metadata/gbif-range-r">10.16904/envidat.352.</a>

## References

Chauvier, Y., Thuiller, W., Brun, P., Lavergne, S., Descombes, P., Karger, D. N., ... & Zimmermann, N. E. (2021). Influence of climate, soil, and land cover on plant species distribution in the European Alps. Ecological monographs, 91(2), e01433. doi: <a href="https://doi.org/10.1002/ecm.1433">10.1002/ecm.1433</a>

Lyu, L., Leugger, F., Hagen, O., Fopp, F., Boschman, L. M., Strijk, J. S., ... & Pellissier, L. (2022). An integrated high‐resolution mapping shows congruent biodiversity patterns of Fagales and Pinales. New Phytologist, 235(2), doi: <a href="https://doi.org/10.1111/nph.18158">10.1111/nph.18158</a>

Chamberlain, S., Oldoni, D., & Waller, J. (2022). rgbif: interface to the global biodiversity information facility API. doi: <a href="https://doi.org/10.5281/zenodo.6023735">10.5281/zenodo.6023735</a>

Zizka, A., Silvestro, D., Andermann, T., Azevedo, J., Duarte Ritter, C., Edler, D., ... & Antonelli, A. (2019). CoordinateCleaner: Standardized cleaning of occurrence records from biological collection databases. Methods in Ecology and Evolution, 10(5), 744-751. doi: <a href="https://doi.org/10.1111/2041-210X.13152">10.1111/2041-210X.13152</a>

Hijmans, Robert J. "terra: Spatial Data Analysis. R Package Version 1.6-7." (2022). Link to package: <a href="https://cran.r-project.org/web/packages/terra/index.html">Terra - CRAN</a>

Olson, D. M., Dinerstein, E., Wikramanayake, E. D., Burgess, N. D., Powell, G. V. N., Underwood, E. C., D'Amico, J. A., Itoua, I., Strand, H. E., Morrison, J. C., Loucks, C. J., Allnutt, T. F., Ricketts, T. H., Kura, Y., Lamoreux, J. F., Wettengel, W. W., Hedao, P., Kassem, K. R. 2001. Terrestrial ecoregions of the world: a new map of life on Earth. Bioscience 51(11):933-938.  doi: <a href="https://doi.org/10.1641/0006-3568(2001)051[0933:TEOTWA]2.0.CO;2">10.1641/0006-3568(2001)051</a>

Mark D. Spalding, Helen E. Fox, Gerald R. Allen, Nick Davidson, Zach A. Ferdaña, Max Finlayson, Benjamin S. Halpern, Miguel A. Jorge, Al Lombana, Sara A. Lourie, Kirsten D. Martin, Edmund McManus, Jennifer Molnar, Cheri A. Recchia, James Robertson, Marine Ecoregions of the World: A Bioregionalization of Coastal and Shelf Areas, BioScience, Volume 57, Issue 7, July 2007, Pages 573–583. doi: <a href="https://doi.org/10.1641/B570707">10.1641/B570707</a>

Robin Abell, Michele L. Thieme, Carmen Revenga, Mark Bryer, Maurice Kottelat, Nina Bogutskaya, Brian Coad, Nick Mandrak, Salvador Contreras Balderas, William Bussing, Melanie L. J. Stiassny, Paul Skelton, Gerald R. Allen, Peter Unmack, Alexander Naseka, Rebecca Ng, Nikolai Sindorf, James Robertson, Eric Armijo, Jonathan V. Higgins, Thomas J. Heibel, Eric Wikramanayake, David Olson, Hugo L. López, Roberto E. Reis, John G. Lundberg, Mark H. Sabaj Pérez, Paulo Petry, Freshwater Ecoregions of the World: A New Map of Biogeographic Units for Freshwater Biodiversity Conservation, BioScience, Volume 58, Issue 5, May 2008, Pages 403–414. doi: <a href="https://doi.org/https://doi.org/10.1641/B580507">10.1641/B580507</a>
