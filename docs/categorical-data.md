Basic Data Manupulation in R Part 2
================
Matt Herman
2018-01-07

Introduction
------------

Today, we'll go over how some additional data manipulation techniques in R. We'll use functions from the `tidyverse` packages to calculate new variables in a data frame, sort data frames, recode categorical variables, and join multiple data frames. Before we start, a quick review of the functions we talked about last time:

-   `filter()`
-   `select()`
-   `summarize()`
-   `tidyverse`

Great! You remember everything. Before we get going, let's do some brief R maintance. As we talked about last time, there are many packages and functions that we will want to use to do all sorts of interesting things in R. Many of these packages are updated frequently to add new functionality and get rid of bugs.

So we want to make sure we have the latest version of these packages installed on our local machine. The easiest way to do that is to click the "Packages" tab in the lower right hand pane and then click the "Update" button. When the "Update Packages" dialog box pops up, check "Select All" and then click "Update". This may take a few minutes if you have a lot of packages to update. It's good practice to update packages every week or so using this method.

(More) Data Manipulation with the `gss_cat` Dataset
---------------------------------------------------

We'll pick up [where we left off last time](https://github.com/mfherman/r-study-group/blob/master/docs/basic_data_manip.md) and continue using the GSS dataset that is built into the `forcats` package. The very first thing we're going to do is load the `tidyverse` package. Remember from last session that `tidyverse` is a meta-package that includes a bunch of a good packages (including `forcats()`) for data exploration, manipulation, and analysis. In fact, I usually start all my scripts with `library(tidyverse)`. After we load `tidyverse`, tell R we're going to be using the `gss_cat`data.

``` r
library(tidyverse)
#> ── Attaching packages ─────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
#> ✔ ggplot2 2.2.1.9000     ✔ purrr   0.2.4.9000
#> ✔ tibble  1.4.1.9000     ✔ dplyr   0.7.4.9000
#> ✔ tidyr   0.7.2.9000     ✔ stringr 1.3.0     
#> ✔ readr   1.1.1.9000     ✔ forcats 0.2.0.9000
#> ── Conflicts ────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
#> ✖ dplyr::filter() masks stats::filter()
#> ✖ dplyr::lag()    masks stats::lag()
data(gss_cat)
```

Do you remember how to examine a data frame? There are a few different ways. If you just want to look at the first 10 rows you can just type the name of the data frame in the console and hit Enter:

``` r
gss_cat
#> # A tibble: 21,483 x 9
#>     year marital         age race   rincome        part… relig denom tvho…
#>    <int> <fctr>        <int> <fctr> <fctr>         <fct> <fct> <fct> <int>
#>  1  2000 Never married    26 White  $8000 to 9999  Ind,… Prot… Sout…    12
#>  2  2000 Divorced         48 White  $8000 to 9999  Not … Prot… Bapt…    NA
#>  3  2000 Widowed          67 White  Not applicable Inde… Prot… No d…     2
#>  4  2000 Never married    39 White  Not applicable Ind,… Orth… Not …     4
#>  5  2000 Divorced         25 White  Not applicable Not … None  Not …     1
#>  6  2000 Married          25 White  $20000 - 24999 Stro… Prot… Sout…    NA
#>  7  2000 Never married    36 White  $25000 or more Not … Chri… Not …     3
#>  8  2000 Divorced         44 White  $7000 to 7999  Ind,… Prot… Luth…    NA
#>  9  2000 Married          44 White  $25000 or more Not … Prot… Other     0
#> 10  2000 Married          47 White  $25000 or more Stro… Prot… Sout…     3
#> # ... with 21,473 more rows
```

Alternatively, you can run the `View(gss_cat)` command or click on the name of the data frame in the Environment pane in the upper right of R Studio.

### Add new variables with `mutate()`

A common task you might need to do is to create a new variable that is in some way related to existing variables in your data set. This might be something relatively simple like changing hours into minutes or changing a continous variable into a categorical variablr. Or it could be a more complex manipulation that relies on several existing variables.

Let's start by creating a new variable (column) that is hours of television watched per year. There is an existing varible, `tvhours`, which is the number of hours per day each respondent reported watching. Mathematically, this is very simple; we'll just multiply the number of hours watched per day by 365 to get the number of hours per year.

``` r
gss_cat_tv <- gss_cat %>%
  mutate(tvhours_year = tvhours * 365)
gss_cat_tv
#> # A tibble: 21,483 x 10
#>     year marital         age race   rinco… party… relig  denom tvho… tvho…
#>    <int> <fctr>        <int> <fctr> <fctr> <fctr> <fctr> <fct> <int> <dbl>
#>  1  2000 Never married    26 White  $8000… Ind,n… Prote… Sout…    12  4380
#>  2  2000 Divorced         48 White  $8000… Not s… Prote… Bapt…    NA    NA
#>  3  2000 Widowed          67 White  Not a… Indep… Prote… No d…     2   730
#>  4  2000 Never married    39 White  Not a… Ind,n… Ortho… Not …     4  1460
#>  5  2000 Divorced         25 White  Not a… Not s… None   Not …     1   365
#>  6  2000 Married          25 White  $2000… Stron… Prote… Sout…    NA    NA
#>  7  2000 Never married    36 White  $2500… Not s… Chris… Not …     3  1095
#>  8  2000 Divorced         44 White  $7000… Ind,n… Prote… Luth…    NA    NA
#>  9  2000 Married          44 White  $2500… Not s… Prote… Other     0     0
#> 10  2000 Married          47 White  $2500… Stron… Prote… Sout…     3  1095
#> # ... with 21,473 more rows
```

See that new variable at the end of the data frame? That's a lot of hours per year! The `mutate()` function looked at the value of `tvhours` and multiplied by 365, **in each row**. This is the important conceptual difference between `mutate()` and `summarize()`. Remember that we used `summarize()` when we wanted to calculate the mean (for example) for an entire data frame (or vector). In other words, we use `summarize()` when we want to calculate one value for an entire data frame. But we use `mutate()` when we want calculate one value for *each row* in a data frame.

The `mutate()` function has a similar syntax to the the `select()` function we covered last session. The general form of `mutate()` is `mutate(data, new_var = some_calculation)`. And remember, this function will add a new variable with values for each row to the data frame you feed into `mutate()`.

### Add more complex new variables with `mutate()` and `case_when()`

The next example is a more complex use of `mutate()` and also introduces a new function, `case_when()`. Currently, `age` is a continuois numeric variable. But let's say we want to create a new age variable with 3 categories: under 35, 35 to 64, and 65 and over.

``` r
gss_cat_age <- gss_cat_tv %>% 
  mutate(
    age_cat = case_when(
      age < 35 ~ "Young",
      age >=35 & age < 65 ~ "Middle Aged",
      age >= 65 ~ "Old"
      )
    )
gss_cat_age
#> # A tibble: 21,483 x 11
#>     year marit…   age race   rinco… party… relig  denom  tvho… tvho… age_…
#>    <int> <fctr> <int> <fctr> <fctr> <fctr> <fctr> <fctr> <int> <dbl> <chr>
#>  1  2000 Never…    26 White  $8000… Ind,n… Prote… South…    12  4380 Young
#>  2  2000 Divor…    48 White  $8000… Not s… Prote… Bapti…    NA    NA Midd…
#>  3  2000 Widow…    67 White  Not a… Indep… Prote… No de…     2   730 Old  
#>  4  2000 Never…    39 White  Not a… Ind,n… Ortho… Not a…     4  1460 Midd…
#>  5  2000 Divor…    25 White  Not a… Not s… None   Not a…     1   365 Young
#>  6  2000 Marri…    25 White  $2000… Stron… Prote… South…    NA    NA Young
#>  7  2000 Never…    36 White  $2500… Not s… Chris… Not a…     3  1095 Midd…
#>  8  2000 Divor…    44 White  $7000… Ind,n… Prote… Luthe…    NA    NA Midd…
#>  9  2000 Marri…    44 White  $2500… Not s… Prote… Other      0     0 Midd…
#> 10  2000 Marri…    47 White  $2500… Stron… Prote… South…     3  1095 Midd…
#> # ... with 21,473 more rows
```

Alright! We added another new variable to the data frame with values in each row. This new variable is called `age_cat` and has three possible values—`Young`, `Middle Aged`, and `Old`—that correspond to the age of the respondent.

Let's break down how that worked. First, we get a new value for each row because we used `mutate()`. This new value is in a column called `age_cat`. In the `mutate()` call, we are creating a new varible called `age_cat` and then defining how to create this new variable. In the previous example, this defition was the same calculation for each row `tvhours * 365`. But in this case, we conditionally define the value of a new variable depending on the value of an existing variable. So, we look at the `age` variable and if `age` is less than 35, we want the value of `age_cat` to be `Young`. If `age` is betweeen 35 and 64, then the value of `age_cat` will be `Middle Aged` and if `age` is more than 64, then the value of `age_cat` will be `Old`.

The easiest way to do this is with the `case_when()` funciton. One way to think about `case_when()` ais s a series of if...then statements. If `age` is `some_value`, then `age_cat` is `some_other_value`.

The general sytnax of `case_when()` is `case_when(condition ~ result)`. The condition is most often some sort of logical statement that uses one or more Boolean operator (`==,`!=`,`&lt;`,`&gt;\`, etc.) and the result is what you want the value of the new variable to be. (The only limitation is that if you have more than one

loYou can add as many condition &gt; result pairs as you want in each `case_when()` call by separating them by with commas.

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
