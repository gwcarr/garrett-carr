\#\#Chapter - Statistical Rethinking Notes
================

We consider the case of the test for being a vampire. We want to know
how likely it is that given a positive test, the individual is a
vampire. We model this by the following:

![
Pr(\\textnormal{vampire}\|\\textnormal{positive})
](https://latex.codecogs.com/png.latex?%0APr%28%5Ctextnormal%7Bvampire%7D%7C%5Ctextnormal%7Bpositive%7D%29%0A "
Pr(\textnormal{vampire}|\textnormal{positive})
")

Most people tend to intuit the normal response is to first measure the
number of positive tests, and then count the number of vampires within
that subset of people.

Using the probability rules to define it as something similar, applying
Baye’s Theorem:

$$ Pr( \| ) = =

Pr( \| )Pr() + Pr( \| )(1 - Pr()) $$ We could look at this block, and it
would be very confusing for most people.

In essence, Bayesian Inference allows us to represent the world as it
is, rather than an estimation of true values with random chance error
that is thrown away. Bayesian inference lets us believe that parameters
exist on a probability distribution, while Frequentist theory would have
us limit this variation as a result of sampling error.

Sampling distributions disappear, and are replaced by probability
distributions. This is what people *thought* sampling distributions
were.
