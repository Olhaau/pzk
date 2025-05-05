---
title: R.utils/R/installPackages.R at develop · HenrikBengtsson/R.utils · GitHub
---

# R.utils/R/installPackages.R at develop · HenrikBengtsson/R.utils · GitHub

url:: https://github.com/HenrikBengtsson/R.utils/blob/develop/R/installPackages.R
up: [[sources]]

#source

```r 
#########################################################################/**
# @RdocDefault installPackages
#
# @title "Install R packages by name or URL"
#
# \description{
#   @get "title".
# }
#
# @synopsis
#
# \arguments{
#   \item{pkgs}{A @character @vector specifying the names and/or the URLs
#     of the R packages to be installed.}
#   \item{types}{A @character @vector of corresponding package types.}
#   \item{repos}{A @character @vector of package repository URLs.}
#   \item{...}{Additional arguments passed to @see "utils::install.packages".}
#   \item{destPath}{Path where any downloaded files are saved.}
#   \item{cleanup}{If @TRUE, downloaded and successfully installed package
#     files are removed, otherwise not.}
# }
#
# \value{
#   Returns nothing.
# }
#
# \section{Limitations}{
#   This method cannot install any packages that are already in use.
#   Certain packages are always in use when calling this method, e.g.
#   \pkg{R.methodsS3}, \pkg{R.oo}, and \pkg{R.utils}.
# }
#
# \examples{\dontrun{
#  installPackages("R.rsp")
#  installPackages("https://cran.r-project.org/src/contrib/Archive/R.rsp/R.rsp_0.8.2.tar.gz")
#  installPackages("https://cran.r-project.org/bin/windows/contrib/4.0/R.rsp_0.44.0.zip")
# }}
#
# @author
#
# @keyword file
#*/#########################################################################
setMethodS3("installPackages", "default", function(pkgs, types="auto", repos=getOption("repos"), ..., destPath=".", cleanup=TRUE) {
  # - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  # Validate arguments
  # - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  # Argument 'pkgs':
  pkgs <- Arguments$getCharacters(pkgs)

  # Argument 'types':
  types <- Arguments$getCharacters(types)

  # Argument 'repos':
  if (!is.null(repos)) {
    repos <- Arguments$getCharacters(repos)
  }

  # Argument 'destPath':
  destPath <- Arguments$getWritablePath(destPath)

  # Argument 'cleanup':
  cleanup <- Arguments$getLogical(cleanup)



  # - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  # Infer 'type' for each package
  # - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  defType <- getOption("pkgType")
  types <- rep(types, length.out=length(pkgs))
  for (kk in seq_along(pkgs)) {
    if (types[kk] == "auto") {
      pkg <- pkgs[kk]
      if (isUrl(pkg)) {
        filename <- basename(pkg)
        ext <- gsub("(.*)[.](tar.gz|tgz|zip)$", "\\2", tolower(filename))
        if (ext == "tar.gz") {
          types[kk] <- "source"
        } else if (ext == "tgz") {
          types[kk] <- "mac.binary.leopard"
        } else if (ext == "zip") {
          types[kk] <- "win.binary"
        } else {
          throw("Cannot install R package. Unknown filename extension: ", pkg)
        }
      } else {
        types[kk] <- defType
      }
    }
  } # for (kk ...)


  # Install each package requested
  for (kk in seq_along(pkgs)) {
    pkg <- pkgs[kk]
    type <- types[kk]

    if (isUrl(pkg)) {
      url <- pkg
      filename <- basename(url)

      # Download file
      pathname <- filePath(destPath, filename)
      downloadFile(url, filename=pathname, skip=TRUE)
      if (!isFile(pathname)) {
        throw("Failed to download package file: ", url)
      }

      install.packages(pathname, repos=NULL, type=type, ...)

      if (cleanup) {
        file.remove(pathname)
        if (isFile(pathname)) {
          throw("Failed to remove package file after installation: ", pathname)
        }
      }
    } else {
      install.packages(pkg, repos=repos, type=type, ...)
    }
  } # for (kk ...)

  invisible()
}) # installPackages()
```

[Skip to content](#start-of-content)

## Navigation Menu

* 
* 
* 
* 
* 
* [Pricing](https://github.com/pricing)  

# Saved searches

## Use saved searches to filter your results more quickly



[Sign up](https://github.com/signup?ref_cta=Sign+up&ref_loc=header+logged+out&ref_page=%2F%3Cuser-name%3E%2F%3Crepo-name%3E%2Fblob%2Fshow&source=header-repo&source_repo=HenrikBengtsson%2FR.utils)  
You signed in with another tab or window. [Reload]() to refresh your session. You signed out in another tab or window. [Reload]() to refresh your session. You switched accounts on another tab or window. [Reload]() to refresh your session. Dismiss alert  
Additional navigation options  

## Breadcrumbs

1. [R.utils](https://github.com/HenrikBengtsson/R.utils/tree/develop)
2. /[R](https://github.com/HenrikBengtsson/R.utils/tree/develop/R)

/

# installPackages.R



Copy path  

## Breadcrumbs

1. [R.utils](https://github.com/HenrikBengtsson/R.utils/tree/develop)
2. /[R](https://github.com/HenrikBengtsson/R.utils/tree/develop/R)

/

# installPackages.R



## File metadata and controls

* 
* 

123 lines (109 loc) · 3.58 KB  
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

57

58

59

60

61

62

63

64

65

66

67

68

69

70

71

72

73

74

75

76

77

78

79

80

81

82

83

84

85

86

87

88

89

90

91

92

93

94

95

96

97

98

99

100

101

102

103

104

105

106

107

108

109

110

111

112

113

114

115

116

117

118

119

120

121

122

123  
#########################################################################/**  
# @RdocDefault installPackages  
# @title "Install R packages by name or URL"  
# \item{pkgs}{A @character @vector specifying the names and/or the URLs  
# of the R packages to be installed.}  
# \item{types}{A @character @vector of corresponding package types.}  
# \item{repos}{A @character @vector of package repository URLs.}  
# \item{...}{Additional arguments passed to @see "utils::install.packages".}  
# \item{destPath}{Path where any downloaded files are saved.}  
# \item{cleanup}{If @TRUE, downloaded and successfully installed package  
# files are removed, otherwise not.}  
# This method cannot install any packages that are already in use.  
# Certain packages are always in use when calling this method, e.g.  
# \pkg{R.methodsS3}, \pkg{R.oo}, and \pkg{R.utils}.  
# installPackages("R.rsp")  
# installPackages("https://cran.r-project.org/src/contrib/Archive/R.rsp/R.rsp_0.8.2.tar.gz")  
# installPackages("https://cran.r-project.org/bin/windows/contrib/4.0/R.rsp_0.44.0.zip")  
#*/#########################################################################  
setMethodS3( "installPackages", "default", function( pkgs, types = "auto", repos =getOption( "repos"), ..., destPath = ".", cleanup = TRUE) {  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
pkgs <- Arguments $getCharacters( pkgs)  
types <- Arguments $getCharacters( types)  
repos <- Arguments $getCharacters( repos)  
destPath <- Arguments $getWritablePath( destPath)  
cleanup <- Arguments $getLogical( cleanup)  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
# Infer 'type' for each package  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
defType <- getOption( "pkgType")  
types <- rep( types, length.out =length( pkgs))  
for ( kk in seq_along( pkgs)) {  
if ( types[ kk] == "auto") {  
filename <- basename( pkg)  
ext <- gsub( "(.*)[.](tar.gz|tgz|zip)$", "\\2", tolower( filename))  
} else if ( ext == "tgz") {  
types[ kk] <- "mac.binary.leopard"  
} else if ( ext == "zip") {  
types[ kk] <- "win.binary"  
throw( "Cannot install R package. Unknown filename extension: ", pkg)  
# Install each package requested  
for ( kk in seq_along( pkgs)) {  
filename <- basename( url)  
pathname <- filePath( destPath, filename)  
downloadFile( url, filename = pathname, skip = TRUE)  
if ( !isFile( pathname)) {  
throw( "Failed to download package file: ", url)  
install.packages( pathname, repos = NULL, type = type, ...)  
throw( "Failed to remove package file after installation: ", pathname)  
install.packages( pkg, repos = repos, type = type, ...)


You can’t perform that action at this time.
