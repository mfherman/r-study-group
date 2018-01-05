Basic Data Manupulation in R Part 2
================
Matt Herman
2018-01-05

Introduction
------------

Today, we'll go over how some additional data manipulation techniques manipulation techniques in R. We'll use functions from the `tidyverse` packages to calculate new variables in a data frame, sort data frames, recode categorical variables, and join multiple data frames. Before we start, a quick review of the functions we talked about last time:

-   `filter()`
-   `select()`
-   `summarize()`

Great! You remember everything.

Data Manipulation with the `gss_cat` Dataset
--------------------------------------------

We'll pick up [where we left off last time](https://github.com/mfherman/r-study-group/blob/master/docs/basic_data_manip.md) and continue using we'll work with a GSS dataset that is built into the `forcats` package. To access this data, first load the `forcats` library, then tell R we're going to be using data called `gss_cat`, and then open the data in the viewer to check it out. Try it!

``` r
library(forcats)
data(gss_cat)
View(gss_cat)
```

Great! It's there. It's a data frame! We can also look at the Environment pane in the upper right hand side of R Studio and we see an object called `gss_cat`. Click on the small blue arrow to left of `gss_cat` and you'll get an expanded list of all the variables in the data frame along with the variable types. You can also open the data viewer by clicking on the name of the data frame in the Environment pane.

### Filter Rows with `filter()`

Let's say we want to create a new dataset that is a subset of the main GSS dataset. Maybe we just want Black respondents. Or maybe we just want observations from after 2008. That's a job for the `filter()` function. `filter()` will return rows that match any criteria you feed to `filter()`.

Let's start by creating a new data frame called `gss_black` that contains only respondents who reported their race to be Black.

``` r
library(tidyverse)
#> ── Attaching packages ─────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
#> ✔ ggplot2 2.2.1.9000     ✔ readr   1.1.1.9000
#> ✔ tibble  1.4.1          ✔ purrr   0.2.4.9000
#> ✔ tidyr   0.7.2.9000     ✔ dplyr   0.7.4.9000
#> ✔ ggplot2 2.2.1.9000     ✔ stringr 1.3.0
#> ── Conflicts ────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
#> ✖ dplyr::filter() masks stats::filter()
#> ✖ dplyr::lag()    masks stats::lag()
gss_black <- filter(gss_cat, race == "Black")
gss_black
#> # A tibble: 3,129 x 9
#>     year marital         age race   rincome        part… relig denom tvho…
#>    <int> <fctr>        <int> <fctr> <fctr>         <fct> <fct> <fct> <int>
#>  1  2000 Married          40 Black  $25000 or more Stro… Prot… Bapt…     7
#>  2  2000 Married          45 Black  Not applicable Inde… Prot… Unit…    NA
#>  3  2000 Married          55 Black  Not applicable Not … Prot… Other     1
#>  4  2000 Divorced         36 Black  $15000 - 19999 Stro… None  Not …    NA
#>  5  2000 Divorced         39 Black  Don't know     Inde… None  Not …    NA
#>  6  2000 Never married    27 Black  $25000 or more Stro… Prot… Am b…     4
#>  7  2000 Widowed          43 Black  Not applicable Inde… None  Not …     3
#>  8  2000 Never married    34 Black  $15000 - 19999 Stro… Prot… Nat …    NA
#>  9  2000 Widowed          83 Black  Not applicable Stro… Prot… Unit…    12
#> 10  2000 Widowed          78 Black  Not applicable Stro… Other Not …     4
#> # ... with 3,119 more rows
```

The general syntax of the `filter()` function is `filter(data, logical_statement)`. So in this case, the data we used is the `gss_cat` data frame and the logical statment is `race == "Black"`. Note that we had to use `==`, not `=` because we are doing a comparison, not an assignment. In other words, we are asking R to compare all records in the race column of `gss_cat` to `"Black"` and return the ones where that condition is `TRUE`. Also note we needed to put `"Black"` in quotation marks for this to work.

After we created the object `gss_black`, we printed `gss_black` to get a quick look at the dataset. We can see there are 3,129 observations of 9 variables. In addition, the first 10 rows of the dataset print in the console along with data type of each variable. This is a quick alternative to viewing the entire data frame in the viewer window.

Next, we will further filter the `gss_black` data frame to only include observations in the 2010, 2012, and 2014 surveys.

``` r
gss_black_recent <- filter(gss_black, year > 2008)
gss_black_recent
#> # A tibble: 998 x 9
#>     year marital         age race   rincome        part… relig denom tvho…
#>    <int> <fctr>        <int> <fctr> <fctr>         <fct> <fct> <fct> <int>
#>  1  2010 Divorced         71 Black  Not applicable Stro… Cath… Not …     5
#>  2  2010 Married          78 Black  Not applicable Inde… Prot… Other    NA
#>  3  2010 Never married    40 Black  $10000 - 14999 Stro… None  Not …     4
#>  4  2010 Divorced         46 Black  $25000 or more Ind,… Cath… Not …    NA
#>  5  2010 Widowed          80 Black  Not applicable Not … Prot… Nat …     1
#>  6  2010 Married          31 Black  Refused        Stro… Cath… Not …    NA
#>  7  2010 Never married    NA Black  Refused        No a… Cath… Not …     1
#>  8  2010 Never married    31 Black  $20000 - 24999 Stro… Cath… Not …     4
#>  9  2010 Married          58 Black  $25000 or more Stro… Prot… Bapt…     2
#> 10  2010 Never married    88 Black  Not applicable Not … Prot… Sout…     4
#> # ... with 988 more rows
```

The `filter()` function relies on Boolean logic so you can use all of our usual logical operator friends when filtering:

-   `==` (is equal to)
-   `!=` (is not equal to)
-   `<` (less than)
-   `>` (greater than)
-   `<=` (less than or equal to)
-   `>=` (greater than or equal to)
-   `&` (and)
-   `|` (or)

You can also combine operations into one `filter()` call. For example, let's create a new data frame that is only white Protestants over the age of 50.

``` r
trump_voters <- filter(gss_cat, race == "White" & age > 50 & relig == "Protestant")
trump_voters
#> # A tibble: 4,142 x 9
#>     year marital    age race   rincome        partyid relig  denom   tvho…
#>    <int> <fctr>   <int> <fctr> <fctr>         <fctr>  <fctr> <fctr>  <int>
#>  1  2000 Widowed     67 White  Not applicable Indepe… Prote… No den…     2
#>  2  2000 Married     53 White  $25000 or more Not st… Prote… Other       2
#>  3  2000 Married     52 White  $25000 or more Strong… Prote… Southe…     1
#>  4  2000 Married     51 White  $25000 or more Strong… Prote… United…    NA
#>  5  2000 Widowed     82 White  Not applicable Not st… Prote… Other       3
#>  6  2000 Widowed     83 White  Not applicable Strong… Prote… Episco…    NA
#>  7  2000 Widowed     89 White  Not applicable Not st… Prote… Other …     4
#>  8  2000 Widowed     88 White  Not applicable Strong… Prote… Afr me…    NA
#>  9  2000 Divorced    72 White  Not applicable Strong… Prote… Southe…     7
#> 10  2000 Widowed     82 White  Not applicable Indepe… Prote… Am bap…    NA
#> # ... with 4,132 more rows
```

### Select columns with `select()`

Now that we've filtered our data frames, maybe we also want to get rid of some variables we don't need for our analysis. This is a job for the `select()` function. It helps us select the columns to keep in our data frame and discards the rest.

``` r
gss_small <- select(gss_cat, year, age, race, tvhours)
gss_small
#> # A tibble: 21,483 x 4
#>     year   age race   tvhours
#>    <int> <int> <fctr>   <int>
#>  1  2000    26 White       12
#>  2  2000    48 White       NA
#>  3  2000    67 White        2
#>  4  2000    39 White        4
#>  5  2000    25 White        1
#>  6  2000    25 White       NA
#>  7  2000    36 White        3
#>  8  2000    44 White       NA
#>  9  2000    44 White        0
#> 10  2000    47 White        3
#> # ... with 21,473 more rows
```

You can include as many variables as you want after the `data` argument of `filter()` by separating them with commas. Another nice feature of the `select()` function is the ability to rename columns as you select them. Let's select the same columns as above, but rename tvhours to weekly\_tv.

``` r
gss_rename <- select(gss_cat, year, age, race, weekly_tv = tvhours)
gss_rename
#> # A tibble: 21,483 x 4
#>     year   age race   weekly_tv
#>    <int> <int> <fctr>     <int>
#>  1  2000    26 White         12
#>  2  2000    48 White         NA
#>  3  2000    67 White          2
#>  4  2000    39 White          4
#>  5  2000    25 White          1
#>  6  2000    25 White         NA
#>  7  2000    36 White          3
#>  8  2000    44 White         NA
#>  9  2000    44 White          0
#> 10  2000    47 White          3
#> # ... with 21,473 more rows
```

To generalize, the syntax here is `select(data, new_col_name = old_col_name)`.

### Summary statistics with `summarize()`

We can also calculate summary statistics with the `summarize()` function.

``` r
summarize(gss_cat,
          n = n(),
          age_mean = mean(age, na.rm = TRUE),
          age_med = median(age, na.rm = TRUE),
          tv_mean = mean(tvhours, na.rm = TRUE)
          )
#> # A tibble: 1 x 4
#>       n age_mean age_med tv_mean
#>   <int>    <dbl>   <int>   <dbl>
#> 1 21483     47.2      46    2.98
```

The general form of `summarize()` is `summarize(data, new_col_name = summary_function(variable))`. You can include as many summary variables as you want by adding new variables and summary functions after a comma. In the above example, we are creating four summary variables. One important note here is that I included `na.rm = TRUE` in the `mean()` and `median()` function calls. This ensures that any missing or `NA` data is dropped when computing the summary statistics. In this case, I know there is some missing data in these variables. If we hadn't included `na.rm = TRUE`, the result would be `NA` because R does not know how to generate the mean or median from missing data unless we explicitly tell R what to do with the `NA` values.

Next up is a simple, but powerful tool to extend the `summarize()` function: `group_by()`. Using this function we can generate grouped data summaries. Now we will calculate the same summary statistics as above, but instead of for the whole dataset, we will get means and medians segmented by race.

``` r
group_by(gss_cat, race) %>%
  summarize(
    n = n(),
    age_mean = mean(age, na.rm = TRUE),
    age_med = median(age, na.rm = TRUE),
    tv_mean = mean(tvhours, na.rm = TRUE)
  )
#> # A tibble: 3 x 5
#>   race       n age_mean age_med tv_mean
#>   <fctr> <int>    <dbl>   <int>   <dbl>
#> 1 Other   1959     39.5      37    2.76
#> 2 Black   3129     43.9      42    4.18
#> 3 White  16395     48.7      48    2.77
```

Cool! In this sample, we see that on average, Black people report watching more hours of TV than white people. Note that this is the exact same code as the last summary, except we added `group_by(gss_cat, race) %>%` before the `summarize()` function. This is saying that before you calculate the mean, group the data frame by the `race` variable and then calculate the mean for each group.

Also, note the use of the `%>%` (pipe). We will talk about this in more detail in another session, but the important thing to know is that the pipe is used to chain functions together. We can think of what the pipe operator does as "and then." So in plain English, what we did above was to group `gss_cat` by `race` and then `summarize` n, mean, and median for each group.

Let's make a couple charts before we go
---------------------------------------

One of the most powerful features of R is the many tools available to make beautiful and customizable visualizations. There is so much to learn on this front, but to start, we're just going to make a couple of common and simple charts you could use when exploring a dataset.

We might want to make a histogram to see the age distribution of our sample. That's pretty simple with `ggplot2`, the main package for making static visualizations in R.

``` r
ggplot(gss_cat) +
  geom_histogram(aes(age), binwidth = 1)
#> Warning: Removed 76 rows containing non-finite values (stat_bin).
```

![](./output/figures/age%20histogram-1.png)

Ok, that's kind of boring. Let's spice it up a little and look at the age distribution, but segmented by race. Maybe the white folks in the GSS sample are older than other races? For this, we'll use a variation on the histogram, the density plot. Don't worry too much about the mathematical details of the plot, just know it represents a smoothed version of a histogram.

``` r
ggplot(gss_cat) +
  geom_density(aes(age, fill = race, color = race), alpha = 0.2)
#> Warning: Removed 76 rows containing non-finite values (stat_density).
```

![](./output/figures/age%20density-1.png)

That's more interesting! And pretty colors too! Also, I was right—the white folks are older!

A different way of visualizing this distribution is with a bar chart where the x-axis is race the y-axis is the number of observations in each race.

``` r
ggplot(gss_cat) +
  geom_bar(aes(race))
```

![](./output/figures/race%20bar%20plot-1.png)

To add another variable to this chart, let's also look at the distribution of marital status within each race. We'll create a stacked bar chart where each bar segment represents the proportion that has each marital status. I'll also change the colors and add a title, legend, and appropriate scale.

``` r
ggplot(gss_cat) +
  geom_bar(aes(race, fill = marital), position = "fill") +
  scale_fill_brewer(palette = "Set2", name = element_blank()) +
  scale_y_continuous(labels = scales::percent) +
  labs(
    x = "Race",
    y = element_blank(),
    title = "Marital Status by Race",
    subtitle = "General Social Survey, 2000 - 2014") +
  theme_bw()
```

![](./output/figures/stacked%20bar%20plot-1.png)

There's a lot I threw into that code, but the main point is that `ggplot2` is endlessly customizable. As you add new features and options to a `ggplot` object, you combine the commands with `+`. This acts very much like `%>%` in that it chains multiple lines of code together.

If you want to jump into `ggplot2`, I highly recommend the [visualization chapter from R for Data Science](http://r4ds.had.co.nz/data-visualisation.html).

Ok, that's it for today. More next time on adding new variables, joining data frames, and more!

    #> ⬢ __  _    __   .    ⬡           ⬢  . 
    #>  / /_(_)__/ /_ ___  _____ _______ ___ 
    #> / __/ / _  / // / |/ / -_) __(_-</ -_)
    #> \__/_/\_,_/\_, /|___/\__/_/ /___/\__/ 
    #>      ⬢  . /___/      ⬡      .       ⬢
