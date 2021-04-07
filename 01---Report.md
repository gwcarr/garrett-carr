Data Cleaning - New
================

## New Project

I am working on a project created from a kaggle data set found here:
<https://www.kaggle.com/teertha/personal-loan-modeling>

I switched to this new project for a number of reasons, including:

-   Format is easier to work with
    -   No need to download additional packages, download from their
        website, etc.
-   Simpler to model.
    -   No topcoded values
    -   Raw data
-   Topic is more interesting.
-   Applies to marketing better
-   Response is a logit model

## Data Story

The data set is from Thera Bank customers, including demographic
information, their relationship with the bank, and their response to the
last personal loan campaign. The dataset was generated in a response to
a successful campaign by the bank to increase sign ups for their
personal loan product, with a conversion rate of 9%.

Our question is this: what factors make a consumer more likely to sign
up for a personal loan? How much do each of these factors affect the
final outcome?

With our response variable set as whether a customer signed up for a
personal loan, the following variables are available in data set:

| Variable           | Description                                                                                  |
|--------------------|----------------------------------------------------------------------------------------------|
| ID                 | Unique identifier for each person; Aribtrary numbers                                         |
| Age                | Customer’s age in completed years                                                            |
| Experience         | Number of years of professional experience                                                   |
| Income             | Annual income ($000)                                                                         |
| ZIP Code           | Home Address zip code                                                                        |
| Family             | Family size of customer                                                                      |
| CCAvg              | Avg. spending on credit cards per month ($000)                                               |
| Education          | Education Level. 1: Undergrad; 2: Graduate; 3: Advanced/Professional                         |
| Mortgage           | Value of house mortgage if any. ($000)                                                       |
| Personal Loan      | Did this customer accept the personal loan offered in the last campaign? (response variable) |
| Securities Account | Does the customer have a securities account with the bank?                                   |
| CD Account         | Does the customer have a certificate of deposit (CD) account with the bank?                  |
| Online             | Does the customer use internet banking facilities?                                           |
| CreditCard         | Does the customer use a credit card issued by this Bank?                                     |

There are no empty values in the data set. Lets take a look at the data.

## Data Exploration

    ## # A tibble: 5,000 x 14
    ##    ID      Age Experience Income `ZIP Code` Family CCAvg Education Mortgage
    ##    <chr> <dbl>      <dbl>  <dbl>      <dbl>  <dbl> <dbl>     <dbl>    <dbl>
    ##  1 1        25          1     49      91107      4   1.6         1        0
    ##  2 2        45         19     34      90089      3   1.5         1        0
    ##  3 3        39         15     11      94720      1   1           1        0
    ##  4 4        35          9    100      94112      1   2.7         2        0
    ##  5 5        35          8     45      91330      4   1           2        0
    ##  6 6        37         13     29      92121      4   0.4         2      155
    ##  7 7        53         27     72      91711      2   1.5         2        0
    ##  8 8        50         24     22      93943      1   0.3         3        0
    ##  9 9        35         10     81      90089      3   0.6         2      104
    ## 10 10       34          9    180      93023      1   8.9         3        0
    ## # ... with 4,990 more rows, and 5 more variables: Personal Loan <lgl>,
    ## #   Securities Account <lgl>, CD Account <lgl>, Online <lgl>, CreditCard <lgl>

    ##       ID                 Age          Experience       Income      
    ##  Length:5000        Min.   :23.00   Min.   :-3.0   Min.   :  8.00  
    ##  Class :character   1st Qu.:35.00   1st Qu.:10.0   1st Qu.: 39.00  
    ##  Mode  :character   Median :45.00   Median :20.0   Median : 64.00  
    ##                     Mean   :45.34   Mean   :20.1   Mean   : 73.77  
    ##                     3rd Qu.:55.00   3rd Qu.:30.0   3rd Qu.: 98.00  
    ##                     Max.   :67.00   Max.   :43.0   Max.   :224.00  
    ##     ZIP Code       Family         CCAvg                        Education   
    ##  Min.   : 9307   One  :1472   Min.   : 0.000   Undergraduate        :2096  
    ##  1st Qu.:91911   Two  :1296   1st Qu.: 0.700   Graduate             :1403  
    ##  Median :93437   Three:1010   Median : 1.500   Advanced/Professional:1501  
    ##  Mean   :93153   Four :1222   Mean   : 1.938                               
    ##  3rd Qu.:94608                3rd Qu.: 2.500                               
    ##  Max.   :96651                Max.   :10.000                               
    ##     Mortgage     Personal Loan   Securities Account CD Account     
    ##  Min.   :  0.0   Mode :logical   Mode :logical      Mode :logical  
    ##  1st Qu.:  0.0   FALSE:4520      FALSE:4478         FALSE:4698     
    ##  Median :  0.0   TRUE :480       TRUE :522          TRUE :302      
    ##  Mean   : 56.5                                                     
    ##  3rd Qu.:101.0                                                     
    ##  Max.   :635.0                                                     
    ##    Online        CreditCard     
    ##  Mode :logical   Mode :logical  
    ##  FALSE:2016      FALSE:3530     
    ##  TRUE :2984      TRUE :1470     
    ##                                 
    ##                                 
    ## 

The data set looks good. Lets take a closer look at our distributions.
I’m curious about the customer demographics.

``` r
ggplot(data = data) +
  geom_histogram(aes(x = Age, color = Education), binwidth = 1) + 
  labs(title = "Customer Demographics", y = "Count") +
  theme_bw()
```

<img src="01---Report_files/figure-gfm/Customer Demographics-1.png" style="display: block; margin: auto;" />

``` r
summary(data$Age)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   23.00   35.00   45.00   45.34   55.00   67.00

It appears we have a good spread of ages and education levels, with a
minimum age of 23, and a max of 67. It also appears we have a roughly
proportional number of different Education levels across our data set.  
This will make for some good predictions.

Lets look at the spread of income next.

``` r
ggplot(data, mapping = aes(x = Income)) +
  geom_density() +
  labs(x = "Income per Year (in Thousands $)", y = NULL, title = "Customer Income")
```

<img src="01---Report_files/figure-gfm/Income-1.png" style="display: block; margin: auto;" />

``` r
summary(data$Income)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    8.00   39.00   64.00   73.77   98.00  224.00

It appears our incomes are heavily right skewed, which is hardly
surprising. Taking a look at mortgage next:

``` r
ggplot(data) +
  geom_density(aes(x = Mortgage)) +
  xlim(c(1, 600)) # Removed large number of zeros from displaying
```

    ## Warning: Removed 3466 rows containing non-finite values (stat_density).

<img src="01---Report_files/figure-gfm/Mortgage-1.png" style="display: block; margin: auto;" />
Please note, I removed a large number of values that were zero. Also
extremely right skewed, with a majority having paid off their mortgage.
It may be this data set is only counting mortgages with the bank, so
maybe it’s not the most reliable predictor.

``` r
ggplot(data, mapping = aes(y = Income)) +
  geom_boxplot(aes(x = Family))
```

<img src="01---Report_files/figure-gfm/Family Stats-1.png" style="display: block; margin: auto;" />

``` r
ggplot(data, mapping = aes(x = Income)) +
  geom_histogram(fill = "white", color = "black", bins = 100) +
  facet_grid(Family ~ .) +
  labs(title = "Income Distribution vs. Family Size", y = NULL) +
  theme_bw()
```

<img src="01---Report_files/figure-gfm/Family Stats-2.png" style="display: block; margin: auto;" />

It appears income is strangely distributed. Accounts holders with family
sizes of 1 and 2 have a much wider range in income levels. This may be
due to couples with double income, so we will have to account for this
effect later.

``` r
# Display mean and sd of Quantitative data, by Zip Code
zip_data <- data %>%
  group_by(`ZIP Code`) %>%
  summarize(across(where(is.double), list(mean = mean, sd = sd)))

zip_data
```

    ## # A tibble: 467 x 11
    ##    `ZIP Code` Age_mean Age_sd Experience_mean Experience_sd Income_mean
    ##         <dbl>    <dbl>  <dbl>           <dbl>         <dbl>       <dbl>
    ##  1       9307     51    NA               25           NA           21  
    ##  2      90005     40.6  11.5             16.4         11.7         66.2
    ##  3      90007     53.8   6.52            29.2          6.21        71.3
    ##  4      90009     49.1  12.9             24.1         13.1         92.4
    ##  5      90011     51    10.5             26.3         10.5         52.7
    ##  6      90016     42    17.0             14.5         13.4         75  
    ##  7      90018     44.2  11.3             19.2         11.0         45.2
    ##  8      90019     42.4  13.6             16.8         12.8        104. 
    ##  9      90024     47.3  10.7             21.9         10.7         70.9
    ## 10      90025     46.3  10.5             20.9         10.6         75.7
    ## # ... with 457 more rows, and 5 more variables: Income_sd <dbl>,
    ## #   CCAvg_mean <dbl>, CCAvg_sd <dbl>, Mortgage_mean <dbl>, Mortgage_sd <dbl>

``` r
tab_summary <- summary(zip_data)
tbl <- tab_summary %>% 
  knitr::kable(padding = 1 ) %>%
  str_replace_all(pattern = "(?<replace>((?<=\\|)((\\w+).?( +)?(\\w+.?)?( +)?:( +)?)))", replacement = " ") %>% 
  str_split(pattern = "( +)?\\|( +)?")

tbl <- tbl %>%  as_tibble(.name_repair = 'minimal')
tbl[,1] <- NULL
tbl[,1] <- NULL
names(tbl) <- c("Min", "1st Q", "Median", "Mean", "3rd Q", "Max", "NA's")
tbl %>% knitr::kable(padding = 1, caption = "Data variation by Zip Code")
```

| Min    | 1st Q  | Median  | Mean    | 3rd Q   | Max     | NA’s |
|:-------|:-------|:--------|:--------|:--------|:--------|:-----|
|        |        |         |         |         |         |      |
|        |        |         |         |         |         |      |
| 9307   | 91736  | 93014   | 92898   | 94605   | 96651   | NA   |
| 27.00  | 42.50  | 45.14   | 45.16   | 47.89   | 63.00   | NA   |
| 0.7071 | 9.4512 | 11.3383 | 11.0645 | 12.8215 | 26.1630 | 11   |
| 0.00   | 17.31  | 20.00   | 19.91   | 22.56   | 37.00   | NA   |
| 0.000  | 9.447  | 11.393  | 11.034  | 12.788  | 26.163  | 11   |
| 9.00   | 59.17  | 71.17   | 72.56   | 84.73   | 142.00  | NA   |
| 0.00   | 30.81  | 43.40   | 42.21   | 52.46   | 108.19  | 11   |
| 0.000  | 1.467  | 1.875   | 1.940   | 2.278   | 7.600   | NA   |
| 0.000  | 1.050  | 1.504   | 1.592   | 2.056   | 4.985   | 11   |
| 0.00   | 27.74  | 52.20   | 57.81   | 77.68   | 266.20  | NA   |
| 0.00   | 55.75  | 84.58   | 87.67   | 116.20  | 287.09  | 11   |
|        |        |         |         |         |         |      |

Data variation by Zip Code

``` r
# 
# 
# plot1 <- ggplot(zip_data, mapping = aes(x = Age_mean, y = Age_sd)) +
#   geom_point() +
#   geom_density_2d() +
#   theme(aspect.ratio = 1) +
#   labs(title = "Age", x = "Mean", y = "StdDev")
# 
# plot2 <- ggplot(zip_data, mapping = aes(x = Experience_mean, y = Experience_sd)) +
#   geom_point() +
#   geom_density_2d() +
#   theme(aspect.ratio = 1) +
#   labs(title = "Experience", x = "Mean", y = "StdDev")
# 
# plot3 <- ggplot(zip_data, mapping = aes(x = Income_mean, y = Income_sd)) +
#   geom_point() +
#   geom_density_2d() +
#   theme(aspect.ratio = 1) +
#   labs(title = "Income", x = "Mean", y = "StdDev")
# 
# plot4 <- ggplot(zip_data, mapping = aes(x = CCAvg_mean, y = CCAvg_sd)) +
#   geom_point() +
#   geom_density_2d() +
#   theme(aspect.ratio = 1) +
#   labs(title = "CCAvg", x = "Mean", y = "StdDev")
# 
# plot5 <- ggplot(zip_data, mapping = aes(x = Mortgage_mean, y = Income_sd)) +
#   geom_point() +
#   geom_density_2d() +
#   theme(aspect.ratio = 1) +
#   labs(title = "Experience", x = "Mean", y = "StdDev")
# 
# print(list(plot1, plot2, plot3, plot4, plot5))
```

There is a definite effect here of the zip code, just given the
variation.

Lets look at the relationships between the variables:

``` r
data <-
  read_csv(
    './Project/Data/Bank_Personal_Loan_Modelling.csv',
    trim_ws = TRUE,
    col_types = "cddddddddlllll"
  ) 

corrplot::corrplot(cor(data %>% select(-ID)), type = "upper")
```

<img src="01---Report_files/figure-gfm/Correlation Plot-1.png" style="display: block; margin: auto;" />
According to the plot, there may be some collinearity between the
`Income` and `CCAvg` variables, which makes sense, because *if you make
more money*, you are also likely to *spend* more.

Age and Experience are also highly correlated, which also makes sense,
because people with more experience almost always tend to be older.

None of the other variables seem to have any concerning
multicollinearity.

I also note that the presence of a CD account with the Bank also seems
to correlate with accepting the personal loan campaign, so we may be
seeing the effects of some brand loyalty here.

Of all the variables, income seems to be among the best predictors for
the personal loan acceptance. We’ll keep that in mind as we build our
model.
