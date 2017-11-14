Basic Data Manipulation in R
================
Matt Herman
2017-11-14

Introduction
------------

Today, we'll go over some basic data manipulation techniques in R. We'll use functions from the `tidyverse` package to filter, select, and summarize data. Before we start, a quick review from last time. What is a:

-   vector
-   data frame
-   function
-   package
-   `<-`

Great! You remember everything.

Just a quick note on formatting. Whenever I refer to a package or function within the text, I will change the formatting so you know I'm referring to something specific. I'm a package: `dplyr`! I'm a function: `filter()`! Sometimes I'll include a few lines of code and in that case, it will be set off from the main text in a code block like so:

``` r
library(tidyverse)
slow_cars <- mtcars %>%
  filter(hp < 150)
```

Whenever you see code like this, you can run it in your version of R Studio. You should type the code you see here into your R Studio script rather than copying and pasting. It will help you get used to writing code!

Data Manipulation with the `gss_cat` Dataset
--------------------------------------------

Today we're going to manipulate and transform some data and hopefully you can start to see the power of R. For all of the code that follows, we'll work with a GSS dataset that is built into the `forcats` package. To access this data, first load the `forcats` library, then tell R we're going be using data called `gss_cat`, and then open the data in the viewer to check it out.

``` r
library(forcats)
data(gss_cat)
View(gss_cat)
```

Great! It's there. It's a data frame! We can also look at the Environment pane in the upper right hand side of R Studio and we should see an object called `gss_cat`. Click on the small blue arrow to left of `gss_cat` and you'll get a list of all the variables in the data frame along with the variable types. You can also open the data viewer by clicking on the name of the data frame in the Environment pane.

### Filter Rows with `filter()`

Now, let's say we want to create a new dataset that is a subset of the main GSS dataset. Maybe we just want Black respondents. Or maybe we just want observations from after 2008. That's a job for the `filter()` function. `filter()` will return rows that match any criteria you feed to `filter()`.

Let's start by creating a new data frame called `gss_black` that contains only respondents who reported their race to be Black.

``` r
library(tidyverse)
#> -- Attaching packages -------------------------------------------------------------------------------------- tidyverse 1.2.1 --
#> v ggplot2 2.2.1     v readr   1.1.1
#> v tibble  1.3.4     v purrr   0.2.4
#> v tidyr   0.7.2     v dplyr   0.7.4
#> v ggplot2 2.2.1     v stringr 1.2.0
#> -- Conflicts ----------------------------------------------------------------------------------------- tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()
gss_black <- filter(gss_cat, race == "Black")
gss_black
#> # A tibble: 3,129 x 9
#>     year       marital   age   race        rincome          partyid
#>    <int>        <fctr> <int> <fctr>         <fctr>           <fctr>
#>  1  2000       Married    40  Black $25000 or more  Strong democrat
#>  2  2000       Married    45  Black Not applicable      Independent
#>  3  2000       Married    55  Black Not applicable Not str democrat
#>  4  2000      Divorced    36  Black $15000 - 19999  Strong democrat
#>  5  2000      Divorced    39  Black     Don't know      Independent
#>  6  2000 Never married    27  Black $25000 or more  Strong democrat
#>  7  2000       Widowed    43  Black Not applicable      Independent
#>  8  2000 Never married    34  Black $15000 - 19999  Strong democrat
#>  9  2000       Widowed    83  Black Not applicable  Strong democrat
#> 10  2000       Widowed    78  Black Not applicable  Strong democrat
#> # ... with 3,119 more rows, and 3 more variables: relig <fctr>,
#> #   denom <fctr>, tvhours <int>
```

The general syntax to filter rows is `filter(data frame, logical statement)`. After we assigned the object `gss_black`, we ran `gss_black` into the console to get R to quick look at the dataset. We can see there are 3,129 observations of 9 variables. In addition, the first 10 rows of the dataset print in the console along with data type of each variable. Note that we had to use `==`, not `=` to filter because we are doing a comparison, not an assignment. In other words, we are asking R to compare all records in the race column of `gss_cat` to `"Black"`. Also note we needed to put `"Black"` in quotation marks.

Next, we will further filter the `gss_black` data frame to only include observations in the 2010, 2012, and 2014 surveys.

``` r
gss_black_recent <- filter(gss_black, year > 2008)
gss_black_recent
#> # A tibble: 998 x 9
#>     year       marital   age   race        rincome          partyid
#>    <int>        <fctr> <int> <fctr>         <fctr>           <fctr>
#>  1  2010      Divorced    71  Black Not applicable  Strong democrat
#>  2  2010       Married    78  Black Not applicable      Independent
#>  3  2010 Never married    40  Black $10000 - 14999  Strong democrat
#>  4  2010      Divorced    46  Black $25000 or more     Ind,near dem
#>  5  2010       Widowed    80  Black Not applicable Not str democrat
#>  6  2010       Married    31  Black        Refused  Strong democrat
#>  7  2010 Never married    NA  Black        Refused        No answer
#>  8  2010 Never married    31  Black $20000 - 24999  Strong democrat
#>  9  2010       Married    58  Black $25000 or more  Strong democrat
#> 10  2010 Never married    88  Black Not applicable Not str democrat
#> # ... with 988 more rows, and 3 more variables: relig <fctr>,
#> #   denom <fctr>, tvhours <int>
```

The `filter()` function relies on Boolean logic so you can use all of our usual logical operator friends when filtering:

-   `==` (is equal to)
-   `<` (less than)
-   `>` (greater than)
-   `<=` (less than or equal to)
-   `>=` (greater than or equal to)
-   `!` (not)
-   `&` (and)
-   `|` (or)

You can also combine operations into one `filter` command. For example, let's create a new data frame that is only white Protestants over the age of 50.

``` r
trump_voters <- filter(gss_cat, race == "White" & age > 50 & relig == "Protestant")
trump_voters
#> # A tibble: 4,142 x 9
#>     year  marital   age   race        rincome           partyid      relig
#>    <int>   <fctr> <int> <fctr>         <fctr>            <fctr>     <fctr>
#>  1  2000  Widowed    67  White Not applicable       Independent Protestant
#>  2  2000  Married    53  White $25000 or more  Not str democrat Protestant
#>  3  2000  Married    52  White $25000 or more   Strong democrat Protestant
#>  4  2000  Married    51  White $25000 or more Strong republican Protestant
#>  5  2000  Widowed    82  White Not applicable  Not str democrat Protestant
#>  6  2000  Widowed    83  White Not applicable   Strong democrat Protestant
#>  7  2000  Widowed    89  White Not applicable  Not str democrat Protestant
#>  8  2000  Widowed    88  White Not applicable Strong republican Protestant
#>  9  2000 Divorced    72  White Not applicable   Strong democrat Protestant
#> 10  2000  Widowed    82  White Not applicable       Independent Protestant
#> # ... with 4,132 more rows, and 2 more variables: denom <fctr>,
#> #   tvhours <int>
```

### Select columns with `select()`

Now that we've filtered our data frames, maybe we also want to get rid of some variables we don't need for our analysis. This is a job for the `select()` function. It helps us select the columns to keep in our data frame and discards the rest.

``` r
gss_small <- select(gss_cat, year, age, race, tvhours)
gss_small
#> # A tibble: 21,483 x 4
#>     year   age   race tvhours
#>    <int> <int> <fctr>   <int>
#>  1  2000    26  White      12
#>  2  2000    48  White      NA
#>  3  2000    67  White       2
#>  4  2000    39  White       4
#>  5  2000    25  White       1
#>  6  2000    25  White      NA
#>  7  2000    36  White       3
#>  8  2000    44  White      NA
#>  9  2000    44  White       0
#> 10  2000    47  White       3
#> # ... with 21,473 more rows
```

Another nice feature of the `select()` function is the ability to rename columns as you select them. Let's select the same columns as above, but rename tvhours to weekly\_tv.

``` r
gss_rename <- select(gss_cat, year, age, race, weekly_tv = tvhours)
gss_rename
#> # A tibble: 21,483 x 4
#>     year   age   race weekly_tv
#>    <int> <int> <fctr>     <int>
#>  1  2000    26  White        12
#>  2  2000    48  White        NA
#>  3  2000    67  White         2
#>  4  2000    39  White         4
#>  5  2000    25  White         1
#>  6  2000    25  White        NA
#>  7  2000    36  White         3
#>  8  2000    44  White        NA
#>  9  2000    44  White         0
#> 10  2000    47  White         3
#> # ... with 21,473 more rows
```

So to generalize, the syntax is `select(data frame, new_col_name = old_col_name)`.s

### Summary statistics with `summarize()`

We can also calculate some simple summary statistics.

``` r
summarize(gss_cat,
          n = n(),
          age_mean = mean(age, na.rm = TRUE),
          age_med = median(age, na.rm = TRUE),
          tv_mean = mean(tvhours, na.rm = TRUE)
          )
#> # A tibble: 1 x 4
#>       n age_mean age_med  tv_mean
#>   <int>    <dbl>   <int>    <dbl>
#> 1 21483 47.18008      46 2.980771
```

One important note here is that I included `na.rm = TRUE` in the `mean()` and `median()` function calls. This ensures that any missing or `NA` data is dropped when computing the summary statistics. In this case, I know there is some missing data, so if we hadn't included `na.rm = TRUE`, the result would be `NA` because R does not know how to average missing data.

Next up is a simple, but powerful tool to extend the `summarize()` function: `group_by()`. Using this function we can generate grouped data summaries. So now we will calculate the same summary statistics as before, but instead of for the whole dataset, we will get means and medians by race.

``` r
group_by(gss_cat, race) %>%
  summarize(
    n = n(),
    age_mean = mean(age, na.rm = TRUE),
    age_med = median(age, na.rm = TRUE),
    tv_mean = mean(tvhours, na.rm = TRUE)
  )
#> # A tibble: 3 x 5
#>     race     n age_mean age_med  tv_mean
#>   <fctr> <int>    <dbl>   <int>    <dbl>
#> 1  Other  1959 39.48232      37 2.761441
#> 2  Black  3129 43.89727      42 4.178824
#> 3  White 16395 48.72493      48 2.770383
```

Cool! In this sample, we see that on average, Black people report watching more hours of TV than white people. Note that this is the exact same code as the last summary, except we added `group_by(gss_cat, race) %>%` before the `summarize()` function. This is saying that before you calculate the mean, group the data frame by the `race` variable and then calculate the mean for each group.

Also, note the use of the `%>%` (pipe). We will talk about this in more detail in another session, but the important thing to know is that the pipe is used to chain functions together. We can think of what the pipe operator does as "and then." So in plain English, what we did above was to group `gss_cat` by `race` and then `summarize` the n, mean, and median of each group.
