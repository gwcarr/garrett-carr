Data Story
================

## Initial Model

The variables available to me are:

-   ID
-   Age
-   Experience
-   Income
-   ZIP Code
-   Family
-   CCAvg
-   Education
-   Mortgage
-   Personal Loan
-   Securities Account
-   CD Account
-   Online
-   CreditCard

ID is just a simple row index, so that will obviously be excluded.

##### Variables

-   Age
-   Experience
-   Income
-   ZIP Code
-   Family
-   CCAvg
-   Education
-   Mortgage
-   Securities Account
-   CD Account
-   Online
-   CreditCard

##### Response

-   Personal Loan

<!-- -->

    ## Loading required package: rstan

    ## Loading required package: StanHeaders

    ## Loading required package: ggplot2

    ## rstan (Version 2.21.2, GitRev: 2e1f913d3ca3)

    ## For execution on a local, multicore CPU with excess RAM we recommend calling
    ## options(mc.cores = parallel::detectCores()).
    ## To avoid recompilation of unchanged Stan programs, we recommend calling
    ## rstan_options(auto_write = TRUE)

    ## Do not specify '-march=native' in 'LOCAL_CPPFLAGS' or a Makevars file

    ## Loading required package: parallel

    ## rethinking (Version 2.13)

    ## 
    ## Attaching package: 'rethinking'

    ## The following object is masked from 'package:stats':
    ## 
    ##     rstudent

    ## This is cmdstanr version 0.3.0

    ## - Online documentation and vignettes at mc-stan.org/cmdstanr

    ## - CmdStan path set to: C:/Users/fyref/Documents/.cmdstanr/cmdstan-2.26.1

    ## - Use set_cmdstan_path() to change the path

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v tibble  3.1.0     v dplyr   1.0.5
    ## v tidyr   1.1.3     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.1
    ## v purrr   0.3.4

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x tidyr::extract() masks rstan::extract()
    ## x dplyr::filter()  masks stats::filter()
    ## x dplyr::lag()     masks stats::lag()
    ## x purrr::map()     masks rethinking::map()

### Creating The Model

I will initially set the priors as flat. We will start with a small
number of predictors.

### Running the Model

Just using the initial model, to see what the output looks like.
Binomial regression with a logit link function for the liklihood of a
person getting a loan.

``` r
# Create data list
loan_list <- list(
  loan = data$`Personal Loan`,
  age = data$Age,
  income = data$Income,
  ccavg = data$CCAvg
)

# Fitting the Initial Model
fit01 <- ulam(
  alist(
    loan ~ dbinom(1, p),
    logit(p) <-  beta_age * age + beta_inc * income + beta_cc * ccavg,
    beta_age ~ dnorm(0, 0.5),
    beta_inc ~ dnorm(0, 1),
    beta_cc ~ dnorm(0, 1.5)
  ),
  data = loan_list,
  log_lik = TRUE,
  chains = 4,
  cores = 4,
  cmdstan = TRUE
)
```

    ## Compiling Stan program...

    ## Running MCMC with 4 parallel chains, with 1 thread(s) per chain...
    ## 
    ## Chain 1 Iteration:   1 / 1000 [  0%]  (Warmup)

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 3 Rejecting initial value:

    ## Chain 3   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 3   Stan can't start sampling from this initial value.

    ## Chain 4 Rejecting initial value:

    ## Chain 4   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 4   Stan can't start sampling from this initial value.

    ## Chain 4 Rejecting initial value:

    ## Chain 4   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 4   Stan can't start sampling from this initial value.

    ## Chain 4 Rejecting initial value:

    ## Chain 4   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 4   Stan can't start sampling from this initial value.

    ## Chain 4 Rejecting initial value:

    ## Chain 4   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 4   Stan can't start sampling from this initial value.

    ## Chain 4 Rejecting initial value:

    ## Chain 4   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 4   Stan can't start sampling from this initial value.

    ## Chain 2 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 3 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 4 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 3 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 4 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 1 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 2 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 3 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 4 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 1 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 3 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 4 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 2 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 1 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 3 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 4 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 1 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 3 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 3 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 4 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 4 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 2 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 1 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 1 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 3 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 4 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 1 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 2 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 3 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 2 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 4 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 3 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 1 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 2 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 3 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 1 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 2 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 4 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 3 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 3 finished in 147.1 seconds.
    ## Chain 1 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 2 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 4 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 1 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 1 finished in 160.2 seconds.
    ## Chain 2 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 4 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 4 finished in 164.2 seconds.
    ## Chain 2 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 2 finished in 167.3 seconds.
    ## 
    ## All 4 chains finished successfully.
    ## Mean chain execution time: 159.7 seconds.
    ## Total execution time: 168.6 seconds.

Stan didn’t like that very much, probably because the priors are trash,
and not very informative.

I will try again to pick initial priors that fit better with the data.
It’s not worth checking the model, so we are going to iterate again.

### Rethinking the Model

The previous model had too many divergent transitions, so we are going
to be more thoughtful about this model.

Let’s assume there is a default value of a conversion rate of 5%, just
based on my experience in keyword advertising, that would be a good
rate. So, we add a `beta0 ~ Normal(mu = 0.05, sigma = 0.01)`

I also wanted to revisit the DAG for this model to see if I should
include any other variables.

``` r
dag01 <- dagitty("dag {
                 Age -> Loan
                 Income -> Loan
                 Age -> Income
                 }")

coordinates(dag01) <- list(x = c(Age = 0, Loan = 1, Income = 2), y = c(Age = 0, Loan = 1, Income = 0))
plot(dag01)
```

![](Data-Story_files/figure-gfm/DAG%201-1.png)<!-- -->

We have to assume that overall income has *some* influence on wage.
However, this model is complicated by the fact that the reported income
values are overall household income, some of which are most definitely
double income households.

So, drawing our DAG again, this time including the variables `Family`
and `CCAvg`.

``` r
dag02 <- dagitty("dag {
                 Age -> Loan
                 Income -> Loan
                 Age -> Income
                 Age -> Family
                 Income -> Credit
                 Family -> Income
                 Credit -> Loan
                 }")

coordinates(dag02) <- list(x = c(Age = 2, Loan = 2, Income = 1, Credit = 0, Family = 0 ), 
                           y = c(Age = 0, Loan = 2, Income = 1, Credit = 2, Family = 0))
plot(dag02)
```

![](Data-Story_files/figure-gfm/DAG%202-1.png)<!-- -->

I also want to measure the difference of the model with and without the
`CCAvg` variable to see if our model makes some better predictions.

``` r
fit02 <- ulam(
  alist(
    loan ~ dbinom(1, p),
    logit(p) <- beta0 + beta_age * age + beta_inc * income,
    beta0 ~ dnorm(0.05, 0.01),
    beta_age ~ dnorm(0, 0.5),
    beta_inc ~ dnorm(0, 1)
  ),
  data = loan_list,
  log_lik = TRUE,
  chains = 4,
  cores = 4,
  cmdstan = TRUE
)
```

    ## Compiling Stan program...

    ## Running MCMC with 4 parallel chains, with 1 thread(s) per chain...

    ## Chain 1 Rejecting initial value:

    ## Chain 1   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 1   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 2 Rejecting initial value:

    ## Chain 2   Log probability evaluates to log(0), i.e. negative infinity.

    ## Chain 2   Stan can't start sampling from this initial value.

    ## Chain 1 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 2 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 3 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 4 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 1 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 2 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 4 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 3 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 1 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 1 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 2 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 4 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 3 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 1 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 4 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 1 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 1 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 2 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 4 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 3 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 1 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 4 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 4 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 2 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 2 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 1 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 2 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 4 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 1 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 3 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 4 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 1 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 2 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 4 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 1 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 1 finished in 106.8 seconds.
    ## Chain 2 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 4 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 2 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 2 finished in 118.0 seconds.
    ## Chain 4 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 4 finished in 122.2 seconds.
    ## Chain 3 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 3 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 3 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 3 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 3 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 3 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 3 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 3 finished in 317.6 seconds.
    ## 
    ## All 4 chains finished successfully.
    ## Mean chain execution time: 166.2 seconds.
    ## Total execution time: 318.9 seconds.

    ## 
    ## Warning: 467 of 2000 (23.0%) transitions ended with a divergence.
    ## This may indicate insufficient exploration of the posterior distribution.
    ## Possible remedies include: 
    ##   * Increasing adapt_delta closer to 1 (default is 0.8) 
    ##   * Reparameterizing the model (e.g. using a non-centered parameterization)
    ##   * Using informative or weakly informative prior distributions

I think Stan liked this model much better. Let’s take a look at what we
have here:

``` r
# precis(fit02)
# show(fit02)
# 
# prior02 <- extract.prior(fit02)
# 
# p <- link(fit02, post = prior02, data = list(age = c(-2, 2)))
```
