02Chapter - Statistical Rethinking Notes
================

## 2.2: Building a Model

The plausibility of (your assumption) after seeing (actual data) is
proportional to the ways that (your assumption) can produce (actual
data) multiplied by the prior plausibility of (your assumption).

An example of computing plausibilities on the garden of forking data:

``` r
# This is the raw counts of possible paths for the data in perspective of all our
# possible assumptions.
ways <- c(0, 3, 8, 9, 0)

# We compute the probabilities of each by dividing by the total number of possibilities.

ways/sum(ways)
```

    ## [1] 0.00 0.15 0.40 0.45 0.00

### Globe Tossing

Picking a prior is important, and the prior assumption can be flat.
Typically, this is used to mean that all possibilities are equally
likely in the scenario.

Sometimes it might be better to pick a couple different priors and try
them all.

In the example of the water vs land on a globe, the distribution of
probabilities is binomial, because there are only two possibilites;
landing on water, or on land.

We look at the grid approximation of the model as shown below:

``` r
# define grid
p_grid <- seq(from = 0, to = 1, length.out = 20)

# define prior
prior <- rep(1, 20)

# compute likelihood at each value in grid
likelihood <- dbinom(6, size = 9, prob = p_grid)

# compute product of likelihood and prior
unstd.posterior <- likelihood * prior

# standardize the posterior, so it sums to 1
posterior <- unstd.posterior/sum(unstd.posterior)

# Looking at the plot:
plot(p_grid, posterior, type = "b",
     xlab = "probability of water", ylab = "posterior probability")
mtext("20 points")
```

![](02-Statistical-Rethinking-Notes_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

Lets look at if we change the prior:

``` r
# define grid
p_grid <- seq(from = 0, to = 1, length.out = 20)

# define prior
prior <- ifelse(p_grid < 0.5, 0, 1)

# compute likelihood at each value in grid
likelihood <- dbinom(6, size = 9, prob = p_grid)

# compute product of likelihood and prior
unstd.posterior <- likelihood * prior

# standardize the posterior, so it sums to 1
posterior <- unstd.posterior/sum(unstd.posterior)

# Looking at the plot:
plot(p_grid, posterior, type = "b",
     xlab = "probability of water", ylab = "posterior probability")
mtext("20 points")
```

![](02-Statistical-Rethinking-Notes_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Exponential

``` r
# define grid
p_grid <- seq(from = 0, to = 1, length.out = 20)

# define prior
prior <- exp(-5 * abs(p_grid - 0.5))

# compute likelihood at each value in grid
likelihood <- dbinom(6, size = 9, prob = p_grid)

# compute product of likelihood and prior
unstd.posterior <- likelihood * prior

# standardize the posterior, so it sums to 1
posterior <- unstd.posterior/sum(unstd.posterior)

# Looking at the plot:
plot(p_grid, posterior, type = "b",
     xlab = "probability of water", ylab = "posterior probability")
mtext("20 points")
```

![](02-Statistical-Rethinking-Notes_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

### Additions From Probability Theory Class

Integral to the understanding of Bayes Theorem, is the following
identity of conditional probability:

![P(A\|B) = \\frac{P(A\\cap B)}{P(B)}](https://latex.codecogs.com/png.latex?P%28A%7CB%29%20%3D%20%5Cfrac%7BP%28A%5Ccap%20B%29%7D%7BP%28B%29%7D "P(A|B) = \frac{P(A\cap B)}{P(B)}")

Explaining in my own words, basically when we measure the probability of
![A](https://latex.codecogs.com/png.latex?A "A") conditional on
![B](https://latex.codecogs.com/png.latex?B "B"), basically we are
saying that we are redefining the sample space as set
![B](https://latex.codecogs.com/png.latex?B "B").

In corollary to what we discussed in the seminar class, we consider the
probability of both ![A](https://latex.codecogs.com/png.latex?A "A") and
![B](https://latex.codecogs.com/png.latex?B "B") (where
![A](https://latex.codecogs.com/png.latex?A "A") and
![B](https://latex.codecogs.com/png.latex?B "B") intersect) and then we
standardize according to the probability of
![B](https://latex.codecogs.com/png.latex?B "B").

