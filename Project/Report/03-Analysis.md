03 - Analysis
================

## Fixing the Model

So it turns out I was running the model wrong, as I thought I was using
standardized values, when in fact I was not.

I want to check out the relationship between some of the variables
before I continue again.

``` r
data_numeric %>% cor() %>% corrplot::corrplot(type = "upper")
```

<img src="../Figures/Correlation Plot-1.png" style="display: block; margin: auto;" />

``` r
ggplot(data, mapping = aes(x = Experience, y = Income)) +
  geom_jitter(aes(color = Education, alpha = 0.5))
```

<img src="../Figures/Checking Relationships-1.png" style="display: block; margin: auto;" />
It generally appears that many undergraduates make more than the other
education levels at all years of experience. This may be due to the
Double Income effect I talked about earlier.

``` r
ggplot(data, mapping = aes(x = Experience, y = Income)) +
  geom_jitter(aes(color = Education, alpha = 0.5)) +
  facet_wrap(~Family)
```

<img src="../Figures/Family 1-1.png" style="display: block; margin: auto;" />

As suspected, there appears to be a relationship. Many people with a
family size of 3 or higher appear to make much less than undergraduates
who have a family size of 1 or 2. Probably due to the double income
effect.

Assuming Incomes are highly right skewed definitely seems to be the case
for family sizes of 3 and four. I wonder if we use Age rather than
experience what patterns will appear.

``` r
ggplot(data, mapping = aes(x = Age, y = Income)) +
  geom_jitter(aes(color = Education, alpha = 0.5)) +
  facet_wrap(~Family)
```

<img src="../Figures/Family 2-1.png" style="display: block; margin: auto;" />

As expected, the graphs look strikingly similar. I want to look at these
factors in terms of the response.

``` r
ggplot(data, mapping = aes(x = Experience, y = `Personal Loan`)) +
  geom_jitter(aes(color = Education, alpha = Income)) +
  facet_wrap(~Family)
```

<img src="../Figures/Family 3-1.png" style="display: block; margin: auto;" />

I find it interesting that people with family sizes of one and two,
hardly any undergraduate educated folks accepted the personal loan
campaign. Most of them are Graduated. Age or experience level doesn’t
necessicarily have a huge impact here.

I wonder about credit card balance..

``` r
ggplot(data, mapping = aes(x = CCAvg, y = `Personal Loan`)) +
  geom_jitter(aes(color = Education, alpha = Income)) +
  facet_wrap(~Family)
```

<img src="../Figures/Family 4-1.png" style="display: block; margin: auto;" />

Strange gap in the graph on two, centered around 5 grand. Huh.

In general, my what we’ve seen so far seems to imply that for folks with
a family size of one or two, education level seems to be driving factor,
where more educated folks tend to accept a personal loan than
undergraduates.

When we look at families of size 3 or 4, it doesn’t seem to matter as
much. But for those groups, it seems a low CC balance is an indicator
that they probably will not accept a personal loan.

``` r
ggplot(data, mapping = aes(x = CCAvg, y = `Personal Loan`)) +
  geom_jitter(aes(color = Income)) +
  facet_wrap(~Family)
```

<img src="../Figures/Family 5-1.png" style="display: block; margin: auto;" />

Individuals with higher incomes and smaller families tend to have higher
credit card balances, but not always.

Individuals with large families, and low incomes tend to have low credit
card balances, and are less likely to accept a personal loan.

``` r
data %>% 
  select(`Personal Loan`, `Securities Account`, `CD Account`, Online, CreditCard) %>% 
  cor() %>% 
  corrplot::corrplot(method = "circle", type = "upper")
```

<img src="../Figures/CD Account-1.png" style="display: block; margin: auto;" />

``` r
ggplot(data, mapping = aes(y = `Personal Loan`)) +
  geom_jitter(mapping = aes(x = Family )) +
  facet_wrap(~`CD Account`)
```

<img src="../Figures/CD Account-2.png" style="display: block; margin: auto;" />
Looking at the first graph, we see that generally, if someone had a CD
Account with the bank, they also would be more likely get a personal
loan from them. There are also more individuals who seemed equally as
likely to get a personal loan if they already had a CD account.

``` r
ggplot(data, mapping = aes(x = Age, y = Mortgage)) +
  geom_density_2d() +
  geom_jitter(aes(color = Family))
```

<img src="../Figures/Mortgage and Family-1.png" style="display: block; margin: auto;" />

There appears to be a group of individuals who have mortgages closer
around 100 grand, ranging in age from about 30, to 60 years old.

``` r
ggplot(data, mapping = aes(y = `Personal Loan`)) +
  geom_jitter(mapping = aes(x = Mortgage, alpha = Income )) +
  facet_wrap(~`CD Account`)
```

<img src="../Figures/Mortgage vs Response-1.png" style="display: block; margin: auto;" />
People with higher mortgages also tended towards getting a personal
loan. Also people with higher incomes generally had higher mortgages.

``` r
ggplot(data) +
  geom_jitter(aes(y = `Personal Loan`, x = Mortgage)) +
  facet_wrap(~Family)
```

<img src="../Figures/Family: Mortgage vs Response-1.png" style="display: block; margin: auto;" />

``` r
byFamCor <- data_numeric %>% split(.$Family) %>%
  map(select, -Family) %>% 
  map(cor) %>% 
  map(corrplot::corrplot, type = "upper")
```

<img src="../Figures/Correlation by Family size-1.png" style="display: block; margin: auto;" /><img src="../Figures/Correlation by Family size-2.png" style="display: block; margin: auto;" /><img src="../Figures/Correlation by Family size-3.png" style="display: block; margin: auto;" /><img src="../Figures/Correlation by Family size-4.png" style="display: block; margin: auto;" />

Income and CCAvg seems to generally have a larger effect on personal
loan for family sizes of 3 or more.

``` r
ggplot(data) +
  geom_jitter(aes(x = Family, y = CCAvg, color = Income))
```

<img src="../Figures/Family vs CC-1.png" style="display: block; margin: auto;" />
Also of note, that Families of 3 or 4 either have very low credit card
balances (mostly at lower income levels), or relatively high CC
balances.

I selected the following variables: - Family - Income - CCAvg

``` r
loan ~ Binomial(1, p)
logit(p) <- alpha[Family] + alpha[Family]*(CCAvg + Income) + CCAvg + Income
Family ~
CCAvg
Income
```

``` r
data_list <- list(
  loan = as.numeric(data$`Personal Loan`),
  income = standardize(data$Income),
  ccavg = standardize(data$CCAvg),
  family = as.numeric(data$Family)
)
```

And now we fit a baseline model, using varying intercepts based on
Family size:

``` r
fit3 <- ulam(
  alist(
    loan ~ dbinom(1, p),
    logit(p) <- alpha[family] + beta_ccavg * ccavg + beta_income * income,
    alpha[family] ~ dnorm(alpha_bar, sigma),
    beta_ccavg ~ dnorm(0, 1.5),
    beta_income ~ dnorm(0, 1.5),
    alpha_bar ~ dnorm(0, 1.5),
    sigma ~ dexp(1)
  ),
  data = data_list,
  log_lik = TRUE,
  chains = 2,
  cores = 4,
  cmdstan = TRUE
)
```

    ## Compiling Stan program...

    ## Running MCMC with 2 chains, at most 4 in parallel, with 1 thread(s) per chain...
    ## 
    ## Chain 1 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 2 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 1 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 2 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 1 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 2 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 1 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 2 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 1 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 1 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 1 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 2 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 2 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 1 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 2 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 1 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 2 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 1 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 2 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 1 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 2 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 1 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 1 finished in 41.8 seconds.
    ## Chain 2 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 2 finished in 42.7 seconds.
    ## 
    ## Both chains finished successfully.
    ## Mean chain execution time: 42.3 seconds.
    ## Total execution time: 43.8 seconds.

``` r
precis(fit3)
```

    ## 4 vector or matrix parameters hidden. Use depth=2 to show them.

    ##                   mean         sd       5.5%      94.5%     n_eff    Rhat4
    ## beta_ccavg   0.1638479 0.05545264  0.0772514  0.2500959 1010.4524 1.000656
    ## beta_income  2.1318785 0.09684323  1.9818872  2.2866148  499.7317 1.000724
    ## alpha_bar   -2.8041780 0.90602937 -3.9998283 -1.1890308  593.7638 1.001373
    ## sigma        1.7590408 0.76253045  0.9014904  3.1702924  558.9379 1.004269

I bet the effect family size is having on income and ccavg is
confounding. Maybe I will try adding an interaction effect.

``` r
fit4 <- ulam(
  alist(
    loan ~ dbinom(1, p),
    logit(p) <- alpha[family] + beta_ccavg * ccavg + beta_income * income + alpha[family] * (ccavg + income),
    alpha[family] ~ dnorm(alpha_bar, sigma),
    beta_ccavg ~ dnorm(0, 1.5),
    beta_income ~ dnorm(0, 1.5),
    alpha_bar ~ dnorm(0, 1.5),
    sigma ~ dexp(1)
  ),
  data = data_list,
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
    ## Chain 2 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 3 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 4 Iteration:   1 / 1000 [  0%]  (Warmup) 
    ## Chain 1 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 2 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 4 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 3 Iteration: 100 / 1000 [ 10%]  (Warmup) 
    ## Chain 4 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 1 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 2 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 3 Iteration: 200 / 1000 [ 20%]  (Warmup) 
    ## Chain 2 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 4 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 1 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 3 Iteration: 300 / 1000 [ 30%]  (Warmup) 
    ## Chain 2 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 4 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 1 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 3 Iteration: 400 / 1000 [ 40%]  (Warmup) 
    ## Chain 2 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 2 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 1 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 1 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 4 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 4 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 3 Iteration: 500 / 1000 [ 50%]  (Warmup) 
    ## Chain 3 Iteration: 501 / 1000 [ 50%]  (Sampling) 
    ## Chain 1 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 2 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 4 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 3 Iteration: 600 / 1000 [ 60%]  (Sampling) 
    ## Chain 1 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 2 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 4 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 3 Iteration: 700 / 1000 [ 70%]  (Sampling) 
    ## Chain 1 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 2 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 4 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 3 Iteration: 800 / 1000 [ 80%]  (Sampling) 
    ## Chain 1 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 2 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 4 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 3 Iteration: 900 / 1000 [ 90%]  (Sampling) 
    ## Chain 1 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 1 finished in 119.4 seconds.
    ## Chain 4 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 4 finished in 126.5 seconds.
    ## Chain 2 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 2 finished in 126.9 seconds.
    ## Chain 3 Iteration: 1000 / 1000 [100%]  (Sampling) 
    ## Chain 3 finished in 127.9 seconds.
    ## 
    ## All 4 chains finished successfully.
    ## Mean chain execution time: 125.2 seconds.
    ## Total execution time: 129.0 seconds.

``` r
precis(fit4)
```

    ## 4 vector or matrix parameters hidden. Use depth=2 to show them.

    ##                  mean        sd       5.5%     94.5%    n_eff    Rhat4
    ## beta_ccavg   4.233756 0.1428038  4.0066573  4.458188 343.3611 1.006605
    ## beta_income  6.024254 0.2060135  5.6856064  6.352645 249.2954 1.005206
    ## alpha_bar   -3.235475 0.6748469 -4.0175560 -1.894071 370.9247 1.008278
    ## sigma        1.018442 0.5757738  0.4646618  2.120612 428.3459 1.002258

Just based off the n\_eff, I think the interaction effect is better left
off.

Though the Rhat suggests that the variables are well fitted, I don’t
think we explored the posterior efficiently enough to deal with all the
data. Part of the data (I would imagine because of the double income
effect) cannot be adequately explained by the model.

``` r
# traceplot(fit3)
# traceplot(fit4)
```
