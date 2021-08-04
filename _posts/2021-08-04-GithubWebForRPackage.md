---
title: 'Create GitHub Websites for Your R Package'
date: 2021-08-04
permalink: /posts/2021/08/GithubWebForRPacakge
tags:
  - R Programming
---

# Prerequisite

1.  Install the R package `usethis`

2.  Have a GitHub account and set up your GitHub credential in R

    1.  <https://usethis.r-lib.org/articles/articles/git-credentials.html>
    2.  TL;DR: see <https://usethis.r-lib.org/articles/articles/git-credentials.html#get-a-personal-access-token-pat>

3.  Create a repo for your R package and connect your local folder to your GH repo

    1.  See <https://usethis.r-lib.org/reference/create_package.html> for creating R package

    2.  See <https://usethis.r-lib.org/reference/use_github.html> for setting up git and github connections

4.  Write your function definitions using `Roxygen2`

# Implementation:

1.  Use the function `usethis::use_pkgdown_github_pages()`

    1.  Just make the selection and you should be done if there was no errors during the process

2.  The problem that I constantly encounter is during creating the branch `gh-pages` where the website contents are stored and updated.

    1.  A lot of time that there could be a error from GitHub side, called Error 500, which prevents to create the branch. In this case, you need to manually create the branch.
    2.  The branch you want to create is called orphan branch, which means there is no contents in that branch. It is a little bit complicated to create a real orphan branch. Hence, I would recommend to create a new branch from your current branch and remove all the contents (files and folders) in that branch. If you are doing it locally, don't forget to sync up your branch to your remote GH repo.
    3.  Also, I highly doubt that it would cause trouble even if you didn't remove all the contents from your `gh-pages` branch. But I didn't try.
    4.  After creating the `gh-pages` manually, run step 1 again. Overwrite pre-existing files if needed. It should work this time.
