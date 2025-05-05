---
title: Make Fake Data • charlatan
---

# Make Fake Data • charlatan

url:: https://docs.ropensci.org/charlatan/
up: [[sources]]

#source

`charlatan` makes fake data, inspired from and borrowing some code from Python’s faker (<https://github.com/joke2k/faker>)

Make fake data for:

* person names
* jobs
* phone numbers
* colors: names, hex, rgb
* credit cards
* DOIs
* numbers in range and from distributions
* gene sequences
* geographic coordinates
* emails
* URIs, URLs, and their parts
* IP addresses
* more coming …

Possible use cases for `charlatan`:

* Students in a classroom setting learning any task that needs a dataset.
* People doing simulations/modeling that need some fake data
* Generate fake dataset of users for a database before actual users exist
* Complete missing spots in a dataset
* Generate fake data to replace sensitive real data with before public release
* Create a random set of colors for visualization
* Generate random coordinates for a map
* Get a set of randomly generated DOIs (Digital Object Identifiers) to assign to fake scholarly artifacts
* Generate fake taxonomic names for a biological dataset
* Get a set of fake sequences to use to test code/software that uses sequence data

Reasons to use `charlatan`:

* Light weight, few dependencies
* Relatively comprehensive types of data, and more being added
* Comprehensive set of languages supported, more being added
* Useful R features such as creating entire fake data.frame’s

## Installation

cran version

dev version  

## high level function

… for all fake data operations  

    x <- https://docs.ropensci.org/charlatan/reference/fraudster.html()
    x$job()
    #> [1] "Corporate investment banker"
    x$name()
    #> [1] "Dr. Garey Hamill"
    x$color_name()
    #> [1] "Ivory"
## locale support

    https://docs.ropensci.org/charlatan/reference/ch_job.html(locale = "fr_FR", n = 3)
    #> [1] "Tailleur de pierre" "Soigneur"           "Ingénieur"
    https://docs.ropensci.org/charlatan/reference/ch_job.html(locale = "hr_HR", n = 3)
    #> [1] "Stalni sudski vještak" "Viši muzejski pedagog" "Kozmetičar"
    https://docs.ropensci.org/charlatan/reference/ch_job.html(locale = "uk_UA", n = 3)
    #> [1] "Льотчик"  "Дипломат" "Педагог"
    https://docs.ropensci.org/charlatan/reference/ch_job.html(locale = "zh_TW", n = 3)
    #> [1] "行政人員"     "珠心算老師"   "飯店工作人員"
## generate a dataset

    https://docs.ropensci.org/charlatan/reference/ch_generate.html()
    #> # A tibble: 10 × 3
    #>    name                    job                                      phone_number
    #>    <chr>                   <chr>                                    <chr>       
    #>  1 Deana Mraz DDS          Printmaker                               +25(2)69696…
    #>  2 Delina Kilback          Administrator, charities/voluntary orga… 04380296996 
    #>  3 Mandi Bailey PhD        Systems analyst                          09381790856 
    #>  4 Ms. Trista Jacobson DVM Pharmacist, hospital                     214-956-893…
    #>  5 King Bartoletti         Teacher, music                           1-312-788-3…
    #>  6 Dr. Ike Gerhold         Audiological scientist                   743.877.3448
    #>  7 Dr. Tatyanna Blanda DVM Manufacturing systems engineer           09691101846 
    #>  8 Antione Grant           Regulatory affairs officer               (406)994-27…
    #>  9 Michal Gutmann          Chartered management accountant          (576)667-99…
    #> 10 Ross Cartwright PhD     Video editor                             07913227887
    https://docs.ropensci.org/charlatan/reference/ch_generate.html("job", "phone_number", n = 30)
    #> # A tibble: 30 × 2
    #>    job                               phone_number        
    #>    <chr>                             <chr>               
    #>  1 Scientist, research (medical)     +63(0)0054265468    
    #>  2 Contracting civil engineer        +97(1)8445952277    
    #>  3 Geneticist, molecular             167-865-4109x84457  
    #>  4 Equities trader                   737.695.1498x1212   
    #>  5 Interior and spatial designer     +49(7)9909862225    
    #>  6 Geophysical data processor        1-884-863-2289x58137
    #>  7 Ophthalmologist                   060-919-7672x6069   
    #>  8 Engineer, agricultural            180-370-0811x1948   
    #>  9 Dealer                            1-838-787-0534      
    #> 10 Environmental health practitioner 884.224.4881        
    #> # ℹ 20 more rows
## job

    https://docs.ropensci.org/charlatan/reference/ch_job.html()
    #> [1] "Set designer"
    https://docs.ropensci.org/charlatan/reference/ch_job.html(10)
    #>  [1] "Actuary"                                    
    #>  [2] "Public house manager"                       
    #>  [3] "Orthoptist"                                 
    #>  [4] "Broadcast engineer"                         
    #>  [5] "Scientist, research (physical sciences)"    
    #>  [6] "Nature conservation officer"                
    #>  [7] "Camera operator"                            
    #>  [8] "Psychologist, prison and probation services"
    #>  [9] "Engineer, communications"                   
    #> [10] "IT sales professional"
## credit cards

    https://docs.ropensci.org/charlatan/reference/ch_credit.html(n = 10)
    #>  [1] "3009338214996378"    "4713530558707"       "3158362208111956356"
    #>  [4] "53355347405525029"   "3720351812179086"    "3044619385256147"   
    #>  [7] "3789072424345968"    "4208219491023"       "3096893682997724534"
    #> [10] "4419344554874021"
## Documentation

All providers have documentation available through the help functions. All providers of the same locales, are linked together, and for every language we have a generic page, for example[?`dutch-language`](https://docs.ropensci.org/charlatan/reference/dutch-language.html).

There are three vignettes, about contributing to this project, what {charlatan} does and a more in depth vignette about creating realistic data.  
* Please [report any issues or bugs](https://github.com/ropensci/charlatan/issues).
* License: MIT
* Get citation information for `charlatan` in R doing `citation(package = 'charlatan')`
* Please note that this package is released with a [Contributor Code of Conduct](https://ropensci.org/code-of-conduct/). By contributing to this project, you agree to abide by its terms.
