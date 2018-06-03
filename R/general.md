# Parallel computing

* Use `foreach` from the `doParallel` package to parallelize *nested* loops:
  ```r
  result <- foreach (i = 1:(n-1), .combine = 'rbind') %:%
    foreach (j = (i+1):n, .combine = 'rbind') %dopar% {
      # expression of i, j, and other variables from enclosing environment...
    }
  ```
  See <https://cran.r-project.org/web/packages/foreach/vignettes/nested.pdf> for more info.
