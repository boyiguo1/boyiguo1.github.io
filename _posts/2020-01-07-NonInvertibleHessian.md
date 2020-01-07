---
title: 'Non-invertibel Hessian Matrix'
date: 2020-01-07
permalink: /posts/2020/01/Non-invertible-Hessian-Matrix/
tags:
  - Optimization
  - Numeric Computation
  - Hessian Matrix
---

During my development of a novel high-dimensional Bayesian model for network data analysis (in my case is microbiome data), I ran into a problem that associates with Hessian Matrix. After using `r optimizing()` function to get the maximum likelihood estimates of the parameters, I needed to invert the Hessian matrix [TODO: add footnote "(or the estimate of a hessian matrix, depdening on the optimizing algorithm used. There are pirmarily three coming with rstan: Newton method, BFGS(quasi-newton algorithm), and LBFGS(quasi-newton algorithm). BFGS and LBFGS use a estimates of the Hessian matrix rather than calculate the Hessian matrix for the sake of computation speed.)"] to calculate the variance/standard error of the estimates.However, the task is not as trivial as it seems like. Even with a "converged" esimation [TODO: add footnote "when the algorithm reaches the default stoping criteria(not the one of maximum iterations, but the tolerance threshold)"] where the estimates are supposed to be the maximums, the Hessian matrix can still be un-invertible or non semi-positive definite. Both these two criteria are necessary to calculate vairance/standard error.

In order to move forward, I needed to locate the root cause of the problem, where I hypothesized couple before doing limited level of reading.
1) The estimation of Hessian matrix was bad. Since both BFGS and LBFGS do not calculate the Hessian matrix, but rather approximating the Hessian matrix in the algorithms, it is possible that the apporximation is bad, which leads to the invertible problem. Then I tried to run the code with Newton method (which I believed and still believe it calculates Hessian matrix). However, it is extremely slow, and seems that the Hessian matrix still is non-invertible (without seriously checking).
2) Another thought I had was increasing the training data sample size(thankfuly, it was a simulation where I could control the sample size.) In a test run, I increased my sample size from 100 to 500 where there were 330 variables where correlation structure exists. The Hessian matrix was invertible and gives variance/standard deivation. (Don't be so happy, it was just one run.) Now I am re-runing the results with larger sample size on cluster with more simulation iterations.

Of course, I had to do more research on how to solve the problem completely or at least understanding why it is a problem, rather than relying on big sample or pure luck (refering to that one test). During my research on the topic, I came across this paper by J Gill and G King[TODO: insert citation], which fantastically explains what to do when Hessian matrix is not invertible, which is the title of the paper. The paper started with introducing the problem of Non-invertibel hessian and provided two alternative approaches (Generalized inverse and importance resample). These alternative is extremely different from the most common Internet solution, modifying the model specification. Please go ahead and read my takeaway from the paper.
