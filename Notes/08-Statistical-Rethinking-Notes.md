Statistical Rethinking: Chapter 8
================

## EDA

Using regularizing priors (“regular priors”), we used in the earlier
chapters for the frogs data set

![
\\begin{tabular}{c c c}
S\_i &\\sim& \\text {Binomial}({\\it N\_i}, {\\it p\_i})
\\\\
\\text {logit}({\\it p\_i}) &=& \\alpha\_{{\\small \\text{TANK}\[i\]}} 
\\\\
{\\it \\alpha\_j} &\\sim& \\text {Normal}(0, 1.5) \\quad \\text { for } {\\it j} = (1, 48)
](https://latex.codecogs.com/png.latex?%0A%5Cbegin%7Btabular%7D%7Bc%20c%20c%7D%0AS_i%20%26%5Csim%26%20%5Ctext%20%7BBinomial%7D%28%7B%5Cit%20N_i%7D%2C%20%7B%5Cit%20p_i%7D%29%0A%5C%5C%0A%5Ctext%20%7Blogit%7D%28%7B%5Cit%20p_i%7D%29%20%26%3D%26%20%5Calpha_%7B%7B%5Csmall%20%5Ctext%7BTANK%7D%5Bi%5D%7D%7D%20%0A%5C%5C%0A%7B%5Cit%20%5Calpha_j%7D%20%26%5Csim%26%20%5Ctext%20%7BNormal%7D%280%2C%201.5%29%20%5Cquad%20%5Ctext%20%7B%20for%20%7D%20%7B%5Cit%20j%7D%20%3D%20%281%2C%2048%29%0A "
\begin{tabular}{c c c}
S_i &\sim& \text {Binomial}({\it N_i}, {\it p_i})
\\
\text {logit}({\it p_i}) &=& \alpha_{{\small \text{TANK}[i]}} 
\\
{\it \alpha_j} &\sim& \text {Normal}(0, 1.5) \quad \text { for } {\it j} = (1, 48)
")
