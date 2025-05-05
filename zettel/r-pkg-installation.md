---
created: 20250505
summary:
---

# r-pkg-installation

## refs

### up

- [[r-pkg]]

### down

## content

### show pkgs

- `installed.packages()`
- `available.packages()`



### only new pkg from specific sources

Example for multiple repos and only installation of new packages:

```r
required_pkgs <- c("skimr", "dplyr")
repos <- c(
    "https://CRAN.R-project.org"
    , "https://community.r-multiverse.org" # for polars
)

for (pkg in required_pkgs){
    if(!(pkg %in% installed.packages())){
        install.packages(pkg, repos = repos)
    }
}
```

### source code

- The source code: [[R.utils_R_installPackages.R at develop _ HenrikBengtsson_R.utils _ GitHub]]
- [[R.utils_R_downloadFile.R at develop _ HenrikBengtsson_R.utils _ GitHub]] is called to download the packages
- uses [[bash-tool-wget]] or [[bash-tool-curl]] depending of availability