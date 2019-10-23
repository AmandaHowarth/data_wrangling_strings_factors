Untitled
================
Amanda Howarth
10/22/2019

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.2.1     ✔ purrr   0.3.2
    ## ✔ tibble  2.1.3     ✔ dplyr   0.8.3
    ## ✔ tidyr   1.0.0     ✔ stringr 1.4.0
    ## ✔ readr   1.3.1     ✔ forcats 0.4.0

    ## ── Conflicts ────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

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
library(p8105.datasets)
```

## Strings & Factors

strings- are exactly what they look like (characters on the screen)
factors- there is underlying structure that R uses in the background for
factors - the words u see in R are lying on top of a numeric structure;
and numeric structured is 1, 2, 3, 4, 5 ordered etc.

strings - make sense for unstructured characterinfo or ALOT of
information/ lots of words that are different from each other

factors - make sense if u have variables that are descriptive with only
a few categorical levels - factors have a few meaningful levels that can
be ordered

read\_csv will not treat anythign as a factor until u tell it to
specifically read.csv will treat all characters as factors (not good)

## Strings

you can do alot with strings. - can concatenate strings together
(putting these two together) - could repace all M’s with L’s or
something

## FACTORS

factors make a differnece when you are thinking about plot ordering and
how u build ur tables - reference category is always the first one - be
aware of the underlying ordering - you may need to relevel the order for
nice plots, etc. - forcats package is way to go for factoring needs
(part of the tidyverse)

## String Manipulation

``` r
string_vec = c("my", "name", "is", "jeff")

str_detect(string_vec, "jeff")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
## str_detect --> to detect the presence or absence of a pattern in a string 
## in my string vector tell me whether the string "jeff" exists 

str_detect(string_vec, "m")
```

    ## [1]  TRUE  TRUE FALSE FALSE

``` r
# found in name and my (TRUE TRUE FALSE FALSE)
```

``` r
## replace one observation with another 
str_replace(string_vec, "jeff", "Jeff")
```

    ## [1] "my"   "name" "is"   "Jeff"

``` r
str_replace(string_vec, "m", "M")
```

    ## [1] "My"   "naMe" "is"   "jeff"

``` r
#creating new string vector 
string_vec = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun actually",
  "it will be fun, i think"
  )

str_detect(string_vec, "i think")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
str_detect(string_vec, "^i think")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
#starts with "I think"

str_detect(string_vec, "i think$")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
# end of line (dollar sign)
```

``` r
string_vec = c(
  "Y'all remember Pres. HW Bush?",
  "I saw a green bush",
  "BBQ and Bushwalking at Molonglo Gorge",
  "BUSH -- LIVE IN CONCERT!!"
  )

str_detect(string_vec,"[Bb]ush")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
# start with capital B or lowercase B, but not all caps
# can also do a range from a-z 
```

``` r
string_vec = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :('
  )

str_detect(string_vec, "^[0-9][a-zA-Z]")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
# you want to detect a number followed by any letter 
```

``` r
string_vec = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66'
  )

str_detect(string_vec, "7.11")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
# want to find everything starting with 7 followed by any character and then followed by 11 (thus AA711 would not work bc no character between 7 and 11)
# the period matches any character at all 
```

``` r
string_vec = c(
  'The CI is [2, 5]',
  ':-]',
  ':-[',
  'I found the answer on pages [6-7]'
  )

str_detect(string_vec, pattern = "\\[")
```

    ## [1]  TRUE FALSE  TRUE  TRUE

``` r
# what u use if u actually want to find a bracket. or want to find a period 
# backslash twice to say u are looking for bracket character 
```

## use strings in practice

``` r
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

table_marj = 
  read_html(nsduh_url) %>% 
  html_nodes(css = "table") %>% 
  .[[1]] %>%
  html_table() %>%
  slice(-1) %>%
  as_tibble()

data_marj = 
  table_marj %>%
  select(-contains("P Value")) %>%
  pivot_longer(
    -State,
    names_to = "age_year", 
    values_to = "percent") %>%
  separate(age_year, into = c("age", "year"), sep = "\\(") %>%
  mutate(
    year = str_replace(year, "\\)", ""),
    percent = str_replace(percent, "[a-c]$", ""),
    percent = as.numeric(percent)) %>%
  filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))

# you could see percent was still being treated as a character vairable so treat it as numeric now 
```

``` r
#below is in regards to a problem on the webpage that uses html but it took my R too long to download  
## you can change page number to 2 to read in the second page, right now it will just be the first page it will read in 
```

## FACTORS

factors are the way to store categorical variables in R - you see the
layer on top (like male and female) but underneath the variables R has
laid a numeric structure

``` r
vec_sex = factor(c("male", "male", "female", "female"))
vec_sex
```

    ## [1] male   male   female female
    ## Levels: female male

``` r
as.numeric(vec_sex)
```

    ## [1] 2 2 1 1

``` r
vec_sex = relevel(vec_sex, ref = "male")
vec_sex
```

    ## [1] male   male   female female
    ## Levels: male female

``` r
as.numeric(vec_sex)
```

    ## [1] 1 1 2 2

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/amandahowarth/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-10-22 21:16:05

    ## file min/max dates: 1869-01-01 / 2019-10-31

    ## file path:          /Users/amandahowarth/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-10-22 21:17:06

    ## file min/max dates: 1965-01-01 / 2019-10-31

    ## file path:          /Users/amandahowarth/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-10-22 21:17:11

    ## file min/max dates: 1999-09-01 / 2019-10-31
