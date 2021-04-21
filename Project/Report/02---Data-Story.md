02 Data Story
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

### Creating The Model

I will initially set the priors as flat. We will start with a small
number of predictors.

``` r
loan ~ binomial(1, p) # Binomial distribution of likelihood to accept the personal loan
logit(p) = beta_age  + beta_income + beta_ccavg  # logit model for acceptance of loan likelihood
beta_age ~ normal(0, 0.01 )          # Effect of Age on loan acceptance 
beta_income ~ normal(0, 0.01)        # Effect of income
beta_ccavg ~ normal(0, 0.01)         # Effect of average credit card balance
```

### Running the Model

Just using the intial model, to see what the output looks like

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
    logit(p) <- beta_age * age + beta_inc * income + beta_cc * ccavg,
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

    ## Chain 1 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 2 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 3 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 4 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 3 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 1 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 4 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 2 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 1 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 3 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 4 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 2 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 4 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 2 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 4 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 1 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 2 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 2 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 4 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 4 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 1 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 4 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 3 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 1 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 1 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 2 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 4 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 3 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 1 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 2 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 4 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 1 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 3 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 3 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 2 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 1 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 4 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 2 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 2 finished in 213.7 seconds.
    ## Chain 1 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 4 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 4 finished in 222.4 seconds.
    ## Chain 3 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 1 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 1 finished in 228.3 seconds.
    ## Chain 3 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 3 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 3 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 3 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 3 finished in 274.6 seconds.
    ## 
    ## All 4 chains finished successfully.
    ## Mean chain execution time: 234.8 seconds.
    ## Total execution time: 276.0 seconds.

    ## 
    ## Warning: 963 of 2000 (48.0%) transitions ended with a divergence.
    ## This may indicate insufficient exploration of the posterior distribution.
    ## Possible remedies include: 
    ##   * Increasing adapt_delta closer to 1 (default is 0.8) 
    ##   * Reparameterizing the model (e.g. using a non-centered parameterization)
    ##   * Using informative or weakly informative prior distributions

Stan didn’t like that very much, probably because the priors are trash,
and not very informative.
