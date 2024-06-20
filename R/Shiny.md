- Tutorial: <https://shiny.posit.co/r/getstarted/shiny-basics/lesson1/index.html>

- To run an app on a remote machine, change to its parent directory, open an R session, and run:

```
library(shiny)
runApp("shiny-app-name", port=9999, host="0.0.0.0", launch.browser=FALSE)
```

