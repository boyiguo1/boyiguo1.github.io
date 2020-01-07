---
title: 'Non-invertibel Hessian Matrix'
date: 2020-01-07
permalink: /posts/2020/01/Non-invertible-Hessian-Matrix/
tags:
  - Optimization
  - Numeric Computation
  - Hessian Matrix
---

During my research on a new Bayesian model for network data analysis, I ran into a problem that associates with Hessian Matrix. After using `r optimizing()` function to get the maximum likelihood estimates of the parameter. In order to get the un-certaintiy measures, which are variance and  standard error, I need to compute the reciporical of negative Hessian matrix (or the estimate of a hessian matrix, depdening on the optimizing algorithm used. There are pirmarily three coming with rstan: Newton method, BFGS(quasi-newton algorithm), and LBFGS(quasi-newton algorithm). BFGS and LBFGS use a estimates of the Hessian matrix rather than calculate the Hessian matrix for the sake of computation speed.). However, other than non-convergence issue from the optimization, I had another problem when inverting Hessian matrix to calculate the variance, i.e. the Hessian matrix is not invertible when the algorithm reaches the default stoping criteria(not the one of maximum iterations, but the tolerance threshold).

In order to move forward, I needed to locate the root of the problem, where I hypothesized couple before doing limited level of reading.
1) The estimation of Hessian matrix was bad. Since both BFGS and LBFGS do not calculate the Hessian matrix, but rather approximating the Hessian matrix in the algorithms, it is possible that the apporximation is bad, which leads to the invertible problem. Then I tried to run the code with Newton method (which I believed and still believe it calculates Hessian matrix). However, it is extremely slow, and seems that the Hessian matrix still is non-invertible (without seriously checking).
2) Another thought I had was increasing the training data sample size(thankfuly, it was a simulation where I could control the sample size.) In a test run, I increased my sample size from 100 to 500 where there were 330 variables where correlation structure exists. The Hessian matrix was invertible and gives variance/standard deivation. (Don't be so happy, it was just one run.) Now I am re-runing the results with larger sample size on cluster with more simulation iterations.

