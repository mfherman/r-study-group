Basic Data Manupulation in R Part 2
================
Matt Herman
2019-02-08

## Introduction

Today, we’ll go over how some additional data manipulation techniques in
R. We’ll use functions from the `tidyverse` packages to calculate new
variables in a data frame, sort data frames, recode categorical
variables, and join multiple data frames. Before we start, a quick
review of the functions we talked about last time:

  - `filter()`
  - `select()`
  - `summarize()`
  - `tidyverse`

Great\! You remember everything. Before we get going, let’s do some
brief R maintenance. As we talked about last time, there are many
packages and functions that we will want to use to do all sorts of
interesting things in R. Many of these packages are updated frequently
to add new functionality and get rid of bugs.

So we want to make sure we have the latest version of these packages
installed on our local machine. The easiest way to do that is to click
the “Packages” tab in the lower right hand pane and then click the
“Update” button. When the “Update Packages” dialog box pops up, check
“Select All” and then click “Update”. This may take a few minutes if
you have a lot of packages to update. It’s good practice to update
packages every week or so using this method.

## (More) Data Manipulation with the `gss_cat` Dataset

We’ll pick up [where we left off last
time](https://github.com/mfherman/r-study-group/blob/master/docs/basic_data_manip.md)
and continue using the GSS dataset that is built into the `forcats`
package. The very first thing we’re going to do is load the `tidyverse`
package. Remember from last session that `tidyverse` is a meta-package
that includes a bunch of a good packages (including `forcats()`) for
data exploration, manipulation, and analysis. In fact, I usually start
all my scripts with `library(tidyverse)`. After we load `tidyverse`,
tell R we’re going to be using the `gss_cat`data.

``` r
library(tidyverse)
#> -- Attaching packages --------------------------------------------- tidyverse 1.2.1 --
#> v ggplot2 3.1.0     v purrr   0.3.0
#> v tibble  2.0.1     v dplyr   0.8.0
#> v tidyr   0.8.2     v stringr 1.3.1
#> v readr   1.3.1     v forcats 0.3.0
#> -- Conflicts ------------------------------------------------ tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()
data(gss_cat)
```

Do you remember how to examine a data frame? There are a few different
ways. If you just want to look at the first 10 rows you can just type
the name of the data frame in the console and hit Enter:

``` r
gss_cat
#> # A tibble: 21,483 x 9
#>     year marital     age race  rincome   partyid    relig   denom   tvhours
#>    <int> <fct>     <int> <fct> <fct>     <fct>      <fct>   <fct>     <int>
#>  1  2000 Never ma~    26 White $8000 to~ Ind,near ~ Protes~ Southe~      12
#>  2  2000 Divorced     48 White $8000 to~ Not str r~ Protes~ Baptis~      NA
#>  3  2000 Widowed      67 White Not appl~ Independe~ Protes~ No den~       2
#>  4  2000 Never ma~    39 White Not appl~ Ind,near ~ Orthod~ Not ap~       4
#>  5  2000 Divorced     25 White Not appl~ Not str d~ None    Not ap~       1
#>  6  2000 Married      25 White $20000 -~ Strong de~ Protes~ Southe~      NA
#>  7  2000 Never ma~    36 White $25000 o~ Not str r~ Christ~ Not ap~       3
#>  8  2000 Divorced     44 White $7000 to~ Ind,near ~ Protes~ Luther~      NA
#>  9  2000 Married      44 White $25000 o~ Not str d~ Protes~ Other         0
#> 10  2000 Married      47 White $25000 o~ Strong re~ Protes~ Southe~       3
#> # ... with 21,473 more rows
```

Alternatively, you can run the `View(gss_cat)` command or click on the
name of the data frame in the Environment pane in the upper right of R
Studio.

### Add new variables with `mutate()`

A common task you might need to do is to create a new variable that is
in some way related to existing variables in your data set. This might
be something relatively simple like changing hours into minutes or
changing a continuous variable into a categorical variable. Or it could
be a more complex manipulation that relies on several existing
variables.

Let’s start by creating a new variable (column) that is hours of
television watched per year. There is an existing variable, `tvhours`,
which is the number of hours per day each respondent reported watching.
Mathematically, this is very simple; we’ll just multiply the number of
hours watched per day by 365 to get the number of hours per year.

``` r
gss_cat_tv <- gss_cat %>%
  mutate(tvhours_year = tvhours * 365)
gss_cat_tv
#> # A tibble: 21,483 x 10
#>     year marital   age race  rincome partyid relig denom tvhours
#>    <int> <fct>   <int> <fct> <fct>   <fct>   <fct> <fct>   <int>
#>  1  2000 Never ~    26 White $8000 ~ Ind,ne~ Prot~ Sout~      12
#>  2  2000 Divorc~    48 White $8000 ~ Not st~ Prot~ Bapt~      NA
#>  3  2000 Widowed    67 White Not ap~ Indepe~ Prot~ No d~       2
#>  4  2000 Never ~    39 White Not ap~ Ind,ne~ Orth~ Not ~       4
#>  5  2000 Divorc~    25 White Not ap~ Not st~ None  Not ~       1
#>  6  2000 Married    25 White $20000~ Strong~ Prot~ Sout~      NA
#>  7  2000 Never ~    36 White $25000~ Not st~ Chri~ Not ~       3
#>  8  2000 Divorc~    44 White $7000 ~ Ind,ne~ Prot~ Luth~      NA
#>  9  2000 Married    44 White $25000~ Not st~ Prot~ Other       0
#> 10  2000 Married    47 White $25000~ Strong~ Prot~ Sout~       3
#> # ... with 21,473 more rows, and 1 more variable: tvhours_year <dbl>
```

See that new variable at the end of the data frame? That’s a lot of
hours per year\! The `mutate()` function looked at the value of
`tvhours` and multiplied by 365, **in each row**. This is the important
conceptual difference between `mutate()` and `summarize()`. Remember
that we used `summarize()` when we wanted to calculate the mean (for
example) for an entire data frame (or vector). In other words, we use
`summarize()` when we want to calculate one value for an entire data
frame. But we use `mutate()` when we want calculate one value for *each
row* in a data frame.

The `mutate()` function has a similar syntax to the the `select()`
function we covered last session. The general form of `mutate()` is
`mutate(data, new_var = some_calculation)`. And remember, this function
will add a new variable with values for each row to the data frame you
feed into `mutate()`.

### Add more complex new variables with `mutate()` and `case_when()`

The next example is a more complex use of `mutate()` and also introduces
a new function, `case_when()`. Currently, `age` is a continuous numeric
variable. But let’s say we want to create a new age variable with 3
categories: under 35, 35 to 64, and 65 and over.

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
#>     year marital   age race  rincome partyid relig denom tvhours
#>    <int> <fct>   <int> <fct> <fct>   <fct>   <fct> <fct>   <int>
#>  1  2000 Never ~    26 White $8000 ~ Ind,ne~ Prot~ Sout~      12
#>  2  2000 Divorc~    48 White $8000 ~ Not st~ Prot~ Bapt~      NA
#>  3  2000 Widowed    67 White Not ap~ Indepe~ Prot~ No d~       2
#>  4  2000 Never ~    39 White Not ap~ Ind,ne~ Orth~ Not ~       4
#>  5  2000 Divorc~    25 White Not ap~ Not st~ None  Not ~       1
#>  6  2000 Married    25 White $20000~ Strong~ Prot~ Sout~      NA
#>  7  2000 Never ~    36 White $25000~ Not st~ Chri~ Not ~       3
#>  8  2000 Divorc~    44 White $7000 ~ Ind,ne~ Prot~ Luth~      NA
#>  9  2000 Married    44 White $25000~ Not st~ Prot~ Other       0
#> 10  2000 Married    47 White $25000~ Strong~ Prot~ Sout~       3
#> # ... with 21,473 more rows, and 2 more variables: tvhours_year <dbl>,
#> #   age_cat <chr>
```

Alright\! We added another new variable to the data frame with values in
each row. This new variable is called `age_cat` and has three possible
values—`Young`, `Middle Aged`, and `Old`—that correspond to the age of
the respondent.

Let’s break down how that worked. First, we get a new value for each row
because we used `mutate()`. This new value is in a column called
`age_cat`. In the `mutate()` call, we are creating a new variable called
`age_cat` and then defining how to create this new variable. In the
previous example, this definition was the same calculation for each row
`tvhours * 365`. But in this case, we conditionally define the value of
a new variable depending on the value of an existing variable. So, we
look at the `age` variable and if `age` is less than 35, we want the
value of `age_cat` to be `Young`. If `age` is between 35 and 64, then
the value of `age_cat` will be `Middle Aged` and if `age` is more than
64, then the value of `age_cat` will be `Old`.

The easiest way to do this is with the `case_when()` function. One way
to think about `case_when()` as s a series of if…then statements. For
examoke, *if* `age` is `< 35`, *then* `age_cat` is `Young`.

The general syntax of `case_when()` is `case_when(condition ~ result)`.
The condition is most often some sort of logical statement that uses one
or more Boolean operator (`==`, `!=`, `<`, `>`, etc.) and the result is
what you want the value of the new variable to be. So in our exampe
above, the first condition, is `age < 35` and the result is `Young`. We
then continue for the two other condtions.
