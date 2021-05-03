---
title: 'Github Pull/Push Reminder in R'
date: 2021-04-30
permalink: /posts/2021/04/GithubReminder
tags:
  - R Programming
  - Version Control
---

I have two computers, a work computer and a personal computer. My dissertation work and methodology development projects reside on both computers. Managing the synchronization of projects on both computers without losing the version control perspective is somewhat challenging. While many cloud storage services, like Dropbox, Box, Microsoft OneDrive, allow you to do it, the version control functionality of these services is sub-optimal, especially if you are a programmer. Thanks to GitHub, the sync of the workspace becomes much easier. However, there is one catch with GitHub: you have to remember to manually "upload" (i.e. push) and "download" (i.e. pull) the changes you make. I constantly forget to do so, such that I have to spend more time merging the changes afterwards. As a "cheap fix" to it, I decide to write R functions that remind me to pull/push the changes from/to GitHub at the beginning and end of every R session. The basic idea is to write two functions, `.Start` and `.Last`, into the user [R profile `.Rprofile` file](https://support.rstudio.com/hc/en-us/articles/360047157094-Managing-R-with-Rprofile-Renviron-Rprofile-site-Renviron-site-rsession-conf-and-repos-conf) such that the two functions will be executed during the initiation of and when quitting an R session. The two functions will prompt messages that remind the user of the R session to pull and push changes from and to GitHub. It works with R Projects.

## Package Dependency
We require the R package `gert`. Depending on if and how you set up your GitHub credential, you may also need the package `gh`. I recommend you use [`usethis`](https://usethis.r-lib.org/index.html) package to [set up your connection with your GitHub account](https://usethis.r-lib.org/articles/articles/git-credentials.html). Since `usethis` package imports `gert` and `gh`, installing `usethis` will also install `gert` and `gh` automatically. 

## TODO:

1. Locate your user `.Rprofile` file and open it with an editor. A short cut is to use `edit_r_profile()` function in `usethis` package by

    ```r
     usethis::edit_r_profile()
    ```

    This should automatically create `.Rprofile` if you don't have one already, and open it for you.


2. Copy the following code to your `.Rprofile`, and save the file. To make the change effective to R, you need to restart R after saving.

    ```r
    ##
    # Session starting reminder:
    #
    
    .First <- function() {
      # Note: only in interactive sessions,
      #       avoid problems when compiling R Markdown et al.
      if(!interactive()) return(NULL)
      
      
      # Note: package dependency  _gert_
      if(!requireNamespace("gert"))
        return(NULL)
      
      
      # Note: sending reminder only if there is git in current or parent folder 
      git_exist <- tryCatch(
        gert::git_find(),
        error = function(e) {
          return(NULL)
        }
      )
      
      if(is.null(git_exist))
        return(NULL)
      else{
        cat("Do you need to fetch changes from GitHub?\n")
        # Note: possibly change to action required
        # response <- readline(prompt="Do you need to fetch changes from GitHub? Press any key to continue.")
        return(NULL)
      }
    }
    
    
    
    
    ##
    # End session reminder:
    #
    
    .Last <- function() {
      if(!interactive()) return(NULL)
      
      if(!requireNamespace("gert"))
        return(NULL)
      
      git_exist <- tryCatch(
        gert::git_find(),
        error = function(e) {
          return(NULL)
        }
      )
      
      if(is.null(git_exist))
        return(NULL)
      
      if(nrow(git_status <- gert::git_status())==0)
        return(NULL)
  
  
      # NOTE: Reminder, typing required
      cat("You have the remaining Git actions to take:\n")
      print(git_status)
      flush.console()
      while(TRUE) {
        response <- readline(prompt="Did you push your changes to GitHub? Y\N\n")
        if(grepl("Y", response, ignore.case=TRUE)) { 
          cat("OK. Bye\n")
          return() 
        } else {
          if(grepl("N", response, ignore.case=TRUE)) { 
            cat("Better save\n")
            stop()
          } else {
            cat("Please answer Yes or No\n")
            flush.console() }
        }
      }
    }
    ```

    When ending the session, if you responded "N" to the question, there would be no reminder prompted when trying to close the session the second time, i.e. seeing the message prompted the second time. It is better to immediately push your changes to GitHub when reading the reminder message the first time.
