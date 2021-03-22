Reading Data
================

``` r
library(ipumsr)
library(R.utils)
```

    ## Loading required package: R.oo

    ## Loading required package: R.methodsS3

    ## R.methodsS3 v1.8.1 (2020-08-26 16:20:06 UTC) successfully loaded. See ?R.methodsS3 for help.

    ## R.oo v1.24.0 (2020-08-26 16:11:58 UTC) successfully loaded. See ?R.oo for help.

    ## 
    ## Attaching package: 'R.oo'

    ## The following object is masked from 'package:R.methodsS3':
    ## 
    ##     throw

    ## The following objects are masked from 'package:methods':
    ## 
    ##     getClasses, getMethods

    ## The following objects are masked from 'package:base':
    ## 
    ##     attach, detach, load, save

    ## R.utils v2.10.1 (2020-08-26 22:50:31 UTC) successfully loaded. See ?R.utils for help.

    ## 
    ## Attaching package: 'R.utils'

    ## The following object is masked from 'package:utils':
    ## 
    ##     timestamp

    ## The following objects are masked from 'package:base':
    ## 
    ##     cat, commandArgs, getOption, inherits, isOpen, nullfile, parse,
    ##     warnings

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.3     v purrr   0.3.4
    ## v tibble  3.0.6     v dplyr   1.0.3
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x tidyr::extract() masks R.utils::extract()
    ## x dplyr::filter()  masks stats::filter()
    ## x dplyr::lag()     masks stats::lag()

``` r
library(haven)
```

## Extracting file

``` r
ifelse(file.exists("nhis_00003.dat.gz"), gunzip("nhis_00003.dat.gz"), "File not found")
```

    ## [1] "File not found"

NOTE: To load data, you must download both the extract’s data and the
DDI and also set the working directory to the folder with these files
(or change the path below).

``` r
if (!require("ipumsr")) stop("Reading IPUMS data into R requires the ipumsr package. It can be installed using the following command: install.packages('ipumsr')")

ddi <- read_ipums_ddi("nhis_00003.xml", lower_vars = TRUE)
data <- read_ipums_micro(ddi)
```

    ## Use of data from IPUMS NHIS is subject to conditions including that users
    ## should cite the data appropriately. Use command `ipums_conditions()` for more
    ## details.

``` r
rm(ddi)
```

## Data cleaning

The Data is included only from the year 2010 on. I may go back and
change this if I find we need more data.

``` r
personal <- data %>%
  select(
    year,
    age,
    sex,
    racea,
    occ,
    usualft,
    incimppoint1,
    incimppoint2,
    incimppoint3,
    incimppoint4,
    incimppoint5,
    earnimpoint1,
    earnimpoint2,
    earnimpoint3,
    earnimpoint4,
    earnimpoint5,
    gotwage,
    height,
    weight
  )

# AGE
## Cleaning up the age values
count_na <- personal$age %>% 
  table() %>% 
  as_tibble() %>% 
  filter( . == 997 | . == 998 | . == 999) # used to count NA's

## Counting values for NA
paste("Setting", sum(count_na$n), "samples in $age, as", NA)
```

    ## [1] "Setting 81 samples in $age, as NA"

``` r
## Replacing non-response
personal$age <- personal$age %>% 
  replace(
    list = which(. == 997 | . == 998 | . == 999),
    values = NA # codes for non-response
  )

# SEX
## Preparing count
count_na <- personal$sex %>% 
  table() %>% 
  as_tibble() %>% 
  filter( . == 7 | . == 8 | . == 9)

## Counting NA
paste("Setting", sum(count_na$n), "samples in $sex, as", NA)
```

    ## [1] "Setting 7 samples in $sex, as NA"

``` r
rm(count_na)

## Replacing values
personal$sex <- personal$sex %>% 
  replace(
    list = which(personal$sex == 7 | personal$sex == 8 | personal$sex == 9),
    values = NA # 7, 8, 9 all codes for non-response
  ) %>% 
  as.character() %>%
  replace( # 1 is the code for Male
    list = which(. == "1"),
    values = "Male"
  ) %>% 
  replace( # 2 is the code for female
    list = which(. == "2"),
    values = "Female"
  )

# RACE
## Lookup using ipums function (from ddi file, which added labels to the data)
race_list <- ipums_val_labels(data$racea)

## Replacing Non-response with NA
personal$racea <- personal$racea %>% 
  replace(
    list = which( . >= 900),
    values = NA
  )

## Adding Main Racial Background as a character
personal <- personal %>%
  left_join(race_list, by = c("racea" = "val"), values = `Main Racial Background`) %>%
  rename(race = lbl) %>%
  select(
    year,
    age,
    sex,
    weight,
    height,
    race,
    occ,
    usualft,
    incimppoint1,
    earnimpoint1,
    gotwage
  )

# GOTWAGE
## Fix gotwage, and change to bool
personal$gotwage <- personal$gotwage %>% 
  as.character() %>% 
  replace(
    list = which(. == 2),
    values = TRUE
  ) %>% 
  replace(
    list = which(. == 1),
    values = FALSE
  ) %>% 
  replace(
    list = which(. == 7 | . == 8 | . == 9),
    values = NA
  ) %>% 
  as.logical()

# FT_Status
## Fix usually full-time status, changed to bool
personal$usualft <- personal$usualft %>% 
  as.character() %>% 
  replace(
    list = which(. == 2),
    values = TRUE
  ) %>% 
  replace(
    list = which(. == 1),
    values = FALSE
  ) %>% 
  replace(
    list = which(. == 7 | . == 8 | . == 9),
    values = NA
  ) %>% 
  as.logical()
```

Data that is used in the project is not meant to be distributed online,
so as such it will not be included in the repo.

Occupation data is included is from the IPUMS.org website.

``` r
library(rvest)
```

    ## Loading required package: xml2

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     pluck

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)

# Getting the list and formatting it from online
occupations_list <- GET(url = 'https://nhis.ipums.org/nhis/10occ.shtml') %>% 
  read_html('//*[@id="main"]/table/tbody') %>% 
  html_table(trim = TRUE) %>% 
  flatten() %>% 
  as_tibble(.name_repair = "minimal") %>% 
  filter(stringr::str_detect(Code, pattern = "[0-9]+$")) # filter out group headings in column

# Replacing non-response values with NA in Lookup table
occupations_list <- occupations_list %>%
  mutate(`Occupation Title` = replace(`Occupation Title`, which(Code == "00" |
                                        Code == "98" | Code == "99"), NA))

# Setting Code as numeric to match with personal df
occupations_list$Code <- as.numeric(occupations_list$Code)

# Adding Occupations data to table
personal <- personal %>%
  left_join(occupations_list, by = c("occ" = "Code")) %>%
  select(
    year,
    age,
    sex,
    weight,
    height,
    race,
    `Occupation Title`,
    usualft,
    gotwage,
    incimppoint1,
    earnimpoint1
  ) %>%
  rename(
    occ_title = `Occupation Title`,
    ft_status = usualft,
    fam_income = incimppoint1,
    indiv_income = earnimpoint1
  )

#Removing occupations lookup table
rm(occupations_list)

personal$weight <- na_if(personal$weight, 0)
personal$height <- na_if(personal$height, 0)

personal <- labelled::remove_labels(personal)
```
