Mini Data Analysis Milestone 2
================

*To complete this milestone, you can edit [this `.rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are commented out with
`<!--- start your work here--->`. When you are done, make sure to knit
to an `.md` file by changing the output in the YAML header to
`github_document`, before submitting a tagged release on canvas.*

# Welcome to your second (and last) milestone in your mini data analysis project!

In Milestone 1, you explored your data, came up with research questions,
and obtained some results by making summary tables and graphs. This
time, we will first explore more in depth the concept of *tidy data.*
Then, you’ll be sharpening some of the results you obtained from your
previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

# Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 55 points (compared to the 45 points
of the Milestone 1): 45 for your analysis, and 10 for your entire
mini-analysis GitHub repository. Details follow.

**Research Questions**: In Milestone 1, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Learning Objectives

By the end of this milestone, you should:

-   Understand what *tidy* data is, and how to create it using `tidyr`.
-   Generate a reproducible and clear report using R Markdown.
-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
library(tidyverse)
library(broom)
library(here)
```

# Task 1: Tidy your data (15 points)

In this task, we will do several exercises to reshape our data. The goal
here is to understand how to do this reshaping with the `tidyr` package.

A reminder of the definition of *tidy* data:

-   Each row is an **observation**
-   Each column is a **variable**
-   Each cell is a **value**

*Tidy’ing* data is sometimes necessary because it can simplify
computation. Other times it can be nice to organize data so that it can
be easier to understand when read manually.

### 2.1 (2.5 points)

Based on the definition above, can you identify if your data is tidy or
untidy? Go through all your columns, or if you have \>8 variables, just
pick 8, and explain whether the data is untidy or tidy.

<!--------------------------- Start your work below --------------------------->

``` r
head(steam_games) # get the first 6 rows of steam_games dataset
```

    ## # A tibble: 6 × 21
    ##      id url          types name  desc_…¹ recen…² all_r…³ relea…⁴ devel…⁵ publi…⁶
    ##   <dbl> <chr>        <chr> <chr> <chr>   <chr>   <chr>   <chr>   <chr>   <chr>  
    ## 1     1 https://sto… app   DOOM  Now in… Very P… Very P… May 12… id Sof… Bethes…
    ## 2     2 https://sto… app   PLAY… PLAYER… Mixed,… Mixed,… Dec 21… PUBG C… PUBG C…
    ## 3     3 https://sto… app   BATT… Take c… Mixed,… Mostly… Apr 24… Harebr… Parado…
    ## 4     4 https://sto… app   DayZ  The po… Mixed,… Mixed,… Dec 13… Bohemi… Bohemi…
    ## 5     5 https://sto… app   EVE … EVE On… Mixed,… Mostly… May 6,… CCP     CCP,CCP
    ## 6     6 https://sto… bund… Gran… Grand … NaN     NaN     NaN     Rockst… Rockst…
    ## # … with 11 more variables: popular_tags <chr>, game_details <chr>,
    ## #   languages <chr>, achievements <dbl>, genre <chr>, game_description <chr>,
    ## #   mature_content <chr>, minimum_requirements <chr>,
    ## #   recommended_requirements <chr>, original_price <dbl>, discount_price <dbl>,
    ## #   and abbreviated variable names ¹​desc_snippet, ²​recent_reviews,
    ## #   ³​all_reviews, ⁴​release_date, ⁵​developer, ⁶​publisher

``` r
rating_positive_and_negative = c("Positive", "Overwhelmingly Positive", "Very Positive", "Mostly Positive", "Mixed", "Mostly Negative", "Very Negative", "Overwhelmingly Negative", "Negative") # initialize the rating labels

subset_steam_games<-steam_games %>%
  mutate(release_year = format(as.Date(release_date, '%b %d, %Y'),'%Y')) %>% # create variable release_year to get year variable
  mutate(languages_number = str_count(languages, ',') + 1) %>% # create variable languages_number by counting languages
  separate(genre, into=c("first_genre"), sep=',', extra = "drop", remove=FALSE) %>% # separate the first genre from genre variable
  separate(all_reviews, into=c("rating"), sep=',', extra = "drop", remove=FALSE) %>% # separate the ratings from all_reviews
  filter(rating %in% rating_positive_and_negative) %>% # remove wrong rating like user reviews and NaN 
  select(id, rating, release_year, languages_number, achievements, first_genre, original_price, discount_price)
subset_steam_games
```

    ## # A tibble: 17,363 × 8
    ##       id rating          release_year language…¹ achie…² first…³ origi…⁴ disco…⁵
    ##    <dbl> <chr>           <chr>             <dbl>   <dbl> <chr>     <dbl>   <dbl>
    ##  1     1 Very Positive   2016                 10      54 Action     20.0    15.0
    ##  2     2 Mixed           2017                 17      37 Action     30.0    NA  
    ##  3     3 Mostly Positive 2018                  4     128 Action     40.0    NA  
    ##  4     4 Mixed           2018                  9      NA Action     45.0    NA  
    ##  5     5 Mostly Positive 2003                  4      NA Action      0      NA  
    ##  6     7 Very Positive   2019                 12      51 Action     60.0    70.4
    ##  7     8 Very Positive   2016                 15      55 Advent…    15.0    17.6
    ##  8     9 Very Positive   2017                 12      34 Strate…    30.0    NA  
    ##  9    10 Mixed           2019                 13      43 Action     50.0    NA  
    ## 10    11 Very Positive   2018                 12      72 Advent…    20.0    NA  
    ## # … with 17,353 more rows, and abbreviated variable names ¹​languages_number,
    ## #   ²​achievements, ³​first_genre, ⁴​original_price, ⁵​discount_price

The eight variables which I choose are id, rating (from all_reviews),
release_year (from release_date), languages_number (from languages),
achievements, first_genre (from genre), original_price, and
discount_price. This data is tidy, because each row is an observation,
each column is a variable, and each cell is a value.

<!----------------------------------------------------------------------------->

### 2.2 (5 points)

Now, if your data is tidy, untidy it! Then, tidy it back to it’s
original state.

If your data is untidy, then tidy it! Then, untidy it back to it’s
original state.

Be sure to explain your reasoning for this task. Show us the “before”
and “after”.

<!--------------------------- Start your work below --------------------------->

*untidy subset_steam_games by creating price_change variable*

``` r
untidy_subset_steam_games<-subset_steam_games %>%
  unite("price_change", original_price:discount_price, remove = TRUE, sep = " to ")
untidy_subset_steam_games
```

    ## # A tibble: 17,363 × 7
    ##       id rating          release_year languages_number achieve…¹ first…² price…³
    ##    <dbl> <chr>           <chr>                   <dbl>     <dbl> <chr>   <chr>  
    ##  1     1 Very Positive   2016                       10        54 Action  19.99 …
    ##  2     2 Mixed           2017                       17        37 Action  29.99 …
    ##  3     3 Mostly Positive 2018                        4       128 Action  39.99 …
    ##  4     4 Mixed           2018                        9        NA Action  44.99 …
    ##  5     5 Mostly Positive 2003                        4        NA Action  0 to NA
    ##  6     7 Very Positive   2019                       12        51 Action  59.99 …
    ##  7     8 Very Positive   2016                       15        55 Advent… 14.99 …
    ##  8     9 Very Positive   2017                       12        34 Strate… 29.99 …
    ##  9    10 Mixed           2019                       13        43 Action  49.99 …
    ## 10    11 Very Positive   2018                       12        72 Advent… 19.99 …
    ## # … with 17,353 more rows, and abbreviated variable names ¹​achievements,
    ## #   ²​first_genre, ³​price_change

*tidy subset_steam_games by separating price_change variable into
original_price variable and discount_price variable*

``` r
tidy_subset_steam_games<-untidy_subset_steam_games %>%
  separate(price_change, into=c("original_price", "discount_price"), sep=' to ', convert=TRUE, remove=TRUE)
tidy_subset_steam_games
```

    ## # A tibble: 17,363 × 8
    ##       id rating          release_year language…¹ achie…² first…³ origi…⁴ disco…⁵
    ##    <dbl> <chr>           <chr>             <dbl>   <dbl> <chr>     <dbl>   <dbl>
    ##  1     1 Very Positive   2016                 10      54 Action     20.0    15.0
    ##  2     2 Mixed           2017                 17      37 Action     30.0    NA  
    ##  3     3 Mostly Positive 2018                  4     128 Action     40.0    NA  
    ##  4     4 Mixed           2018                  9      NA Action     45.0    NA  
    ##  5     5 Mostly Positive 2003                  4      NA Action      0      NA  
    ##  6     7 Very Positive   2019                 12      51 Action     60.0    70.4
    ##  7     8 Very Positive   2016                 15      55 Advent…    15.0    17.6
    ##  8     9 Very Positive   2017                 12      34 Strate…    30.0    NA  
    ##  9    10 Mixed           2019                 13      43 Action     50.0    NA  
    ## 10    11 Very Positive   2018                 12      72 Advent…    20.0    NA  
    ## # … with 17,353 more rows, and abbreviated variable names ¹​languages_number,
    ## #   ²​achievements, ³​first_genre, ⁴​original_price, ⁵​discount_price

First, I use unite function to create price_change variable to show the
price change, then I use separate function to tidy the dataset to the
original format.
<!----------------------------------------------------------------------------->

### 2.3 (7.5 points)

Now, you should be more familiar with your data, and also have made
progress in answering your research questions. Based on your interest,
and your analyses, pick 2 of the 4 research questions to continue your
analysis in the next four tasks:

<!-------------------------- Start your work below ---------------------------->

1.  *How does game’s price affect game’s review? Are cheap games easier
    to get good reviews?*
2.  *What is the relationship between game’s release time and game’s
    languages?*

<!----------------------------------------------------------------------------->

Explain your decision for choosing the above two research questions.

<!--------------------------- Start your work below --------------------------->

I chose these two questions because I think they are more worth
exploring. As for the relationship between game’s price and game’s
review, I found that there are plenty of useful information in
all_rating variable and recent_rating variable which are interesting to
analyze. As for the relationship between game’s release time and game’s
languages, I feel we can discover many interesting things from data over
time, so this question is worth exploring as well.
<!----------------------------------------------------------------------------->

Now, try to choose a version of your data that you think will be
appropriate to answer these 2 questions. Use between 4 and 8 functions
that we’ve covered so far (i.e. by filtering, cleaning, tidy’ing,
dropping irrelevant columns, etc.).

<!--------------------------- Start your work below --------------------------->

``` r
head(steam_games) # get the first 6 rows of steam_games dataset
```

    ## # A tibble: 6 × 21
    ##      id url          types name  desc_…¹ recen…² all_r…³ relea…⁴ devel…⁵ publi…⁶
    ##   <dbl> <chr>        <chr> <chr> <chr>   <chr>   <chr>   <chr>   <chr>   <chr>  
    ## 1     1 https://sto… app   DOOM  Now in… Very P… Very P… May 12… id Sof… Bethes…
    ## 2     2 https://sto… app   PLAY… PLAYER… Mixed,… Mixed,… Dec 21… PUBG C… PUBG C…
    ## 3     3 https://sto… app   BATT… Take c… Mixed,… Mostly… Apr 24… Harebr… Parado…
    ## 4     4 https://sto… app   DayZ  The po… Mixed,… Mixed,… Dec 13… Bohemi… Bohemi…
    ## 5     5 https://sto… app   EVE … EVE On… Mixed,… Mostly… May 6,… CCP     CCP,CCP
    ## 6     6 https://sto… bund… Gran… Grand … NaN     NaN     NaN     Rockst… Rockst…
    ## # … with 11 more variables: popular_tags <chr>, game_details <chr>,
    ## #   languages <chr>, achievements <dbl>, genre <chr>, game_description <chr>,
    ## #   mature_content <chr>, minimum_requirements <chr>,
    ## #   recommended_requirements <chr>, original_price <dbl>, discount_price <dbl>,
    ## #   and abbreviated variable names ¹​desc_snippet, ²​recent_reviews,
    ## #   ³​all_reviews, ⁴​release_date, ⁵​developer, ⁶​publisher

``` r
rating_positive_and_negative = c("Positive", "Overwhelmingly Positive", "Very Positive", "Mostly Positive", "Mixed", "Mostly Negative", "Very Negative", "Overwhelmingly Negative", "Negative") # initialize the rating labels

subset_steam_games<-steam_games %>%
  separate(genre, into=c("first_genre"), sep=',', extra = "drop", remove=FALSE) %>% # separate the first genre from genre variable
  mutate(languages_number = str_count(languages, ',') + 1) %>% # create variable languages_number by counting languages variable
  separate(all_reviews, into=c("all_rating"), sep=',', extra = "drop", remove=FALSE) %>% # separate the ratings from all_reviews
  mutate(release_year = format(as.Date(release_date, '%b %d, %Y'),'%Y')) %>% # get the year of the release_date
  #filter(all_rating %in% rating_positive_and_negative) %>% # remove wrong rating like user reviews and NaN 
  separate(recent_reviews, into=c("recent_rating"), sep=',', extra = "drop", remove=FALSE) %>% # separate the ratings from all_reviews
  #filter(recent_rating %in% rating_positive_and_negative) %>% # remove wrong rating like user reviews and NaN 
  select(id, release_year, all_rating, recent_rating, languages_number, first_genre, original_price, discount_price)
subset_steam_games
```

    ## # A tibble: 40,833 × 8
    ##       id release_year all_rating      recent_r…¹ langu…² first…³ origi…⁴ disco…⁵
    ##    <dbl> <chr>        <chr>           <chr>        <dbl> <chr>     <dbl>   <dbl>
    ##  1     1 2016         Very Positive   Very Posi…      10 Action     20.0    15.0
    ##  2     2 2017         Mixed           Mixed           17 Action     30.0    NA  
    ##  3     3 2018         Mostly Positive Mixed            4 Action     40.0    NA  
    ##  4     4 2018         Mixed           Mixed            9 Action     45.0    NA  
    ##  5     5 2003         Mostly Positive Mixed            4 Action      0      NA  
    ##  6     6 <NA>         NaN             NaN             12 Action     NA      35.2
    ##  7     7 2019         Very Positive   Very Posi…      12 Action     60.0    70.4
    ##  8     8 2016         Very Positive   Very Posi…      15 Advent…    15.0    17.6
    ##  9     9 2017         Very Positive   Very Posi…      12 Strate…    30.0    NA  
    ## 10    10 2019         Mixed           <NA>            13 Action     50.0    NA  
    ## # … with 40,823 more rows, and abbreviated variable names ¹​recent_rating,
    ## #   ²​languages_number, ³​first_genre, ⁴​original_price, ⁵​discount_price

<!----------------------------------------------------------------------------->

# Task 2: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

``` r
game_price_classification <- within(subset_steam_games, {   
  original_price_level <- NA # initialize the variable
  original_price_level[original_price < 5] <- "Very Low" # if original_price < 5, then Very Low
  original_price_level[original_price >= 5 & original_price < 10] <- "Low" # if original_price >= 5 & original_price < 10, then Low
  original_price_level[original_price >= 10 & original_price < 25] <- "Medium" # if original_price >= 10 & original_price < 25, then Medium
  original_price_level[original_price >= 25 & original_price < 60] <- "High" # if original_price >= 25 & original_price < 60, then High
  original_price_level[original_price >= 60] <- "Very High" # if original_price >= 60, then Very High
   } )

rating_positive_and_negative = c("Positive", "Overwhelmingly Positive", "Very Positive", "Mostly Positive", "Mixed", "Mostly Negative", "Very Negative", "Overwhelmingly Negative", "Negative") # initialize the rating labels

game_price_reviews <- game_price_classification %>%
  #separate(all_reviews, into=c("rating"), sep=',', extra = "drop", remove=FALSE) %>% # separate the ratings from all_reviews
  filter(all_rating %in% rating_positive_and_negative) %>% # remove wrong rating like user reviews and NaN 
  group_by(original_price_level) %>% # group by original_price_level
  count(all_rating) %>% # count different kinds of ratings within each group
  drop_na(original_price_level) # drop NA in original_price_level
  
game_price_reviews_plot <- game_price_reviews %>%
  ggplot(aes(x = original_price_level, weight = n)) + 
  geom_bar(aes(fill = all_rating)) +
  xlab("Original Price Level") +
  ylab("Count")
  
game_price_reviews_plot
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

I choose the 4.1.2 plot in mini-project-1 in this part. I add “Very Low”
and “Very High” price levels and change the ranges of
original_price_level because the price distribution of the steam games
is extremely uneven, most steam games are priced under 10 dollars, and
there are very few games’ price over 25 dollars. And I also delete
scale_y\_continuous(trans = ‘log10’) and coord_flip(). Thus, the output
plot may look different with the original one.
<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)

        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.

    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).

        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: Task 1

``` r
game_price_reviews_plot_1 <- game_price_reviews %>%
  ggplot(aes(x=fct_reorder(original_price_level, n, .fun = mean), weight=n)) + 
  geom_bar(aes(fill = all_rating)) +
  xlab("Original Price Level") +
  ylab("Count")
  
game_price_reviews_plot_1
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Here, I choose to reorder the original price level in increasing order
of the number of steam games at each price level. With fct_reorder
function, I can know which price levels have more steam games.
<!----------------------------------------------------------------------------->

<!-------------------------- Start your work below ---------------------------->

**Task Number**: Task 2

``` r
game_price_reviews_plot_2 <- game_price_reviews %>%
  ggplot(aes(x=fct_other(original_price_level, keep = c("Very Low", "Low", "Medium")), weight=n)) + 
  geom_bar(aes(fill = all_rating)) +
  xlab("Original Price Level") +
  ylab("Count")
  
game_price_reviews_plot_2
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Here, I choose to keep “Very Low”, “Low”, and “Medium” variables and
merge “High” and “Very High” variables into one variable “Other”,
because the numbers of data corresponding to “High” and “Very High”
variables are few.

<!----------------------------------------------------------------------------->

# Task 3: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: *What is the relationship between game’s release
time and (the number of) game’s languages?*

**Variable of interest**: *The number of game’s languages*

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.

    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

``` r
game_time_languages <- subset_steam_games %>%
  drop_na(release_year) %>% # drop NA from release_year
  group_by(release_year, languages_number) %>% # group by release_year and languages_number
  summarize(number1 = n(), .groups = "drop")

game_time_languages_final <- game_time_languages %>%
  group_by(release_year) %>%
  drop_na(languages_number) %>%
  drop_na(number1) %>%
  summarize(mean_languages_number = (sum(languages_number*number1)/sum(number1)))

game_time_languages_final$release_year <- as.integer(game_time_languages_final$release_year)
```

Model that makes predictions on mean_languages_number using release_year
variable, using lm() function

``` r
game_time_languages_lm <- lm(mean_languages_number ~ release_year, game_time_languages_final)
game_time_languages_lm
```

    ## 
    ## Call:
    ## lm(formula = mean_languages_number ~ release_year, data = game_time_languages_final)
    ## 
    ## Coefficients:
    ##  (Intercept)  release_year  
    ##    -129.9276        0.0663

t.test of release_year and mean_languages_number

``` r
game_time_languages_test <- t.test(game_time_languages_final$release_year, game_time_languages_final$mean_languages_number)
game_time_languages_test
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  game_time_languages_final$release_year and game_time_languages_final$mean_languages_number
    ## t = 1031.7, df = 43.002, p-value < 2.2e-16
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  1996.21 2004.03
    ## sample estimates:
    ##   mean of x   mean of y 
    ## 2003.000000    2.880135

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

``` r
#game_time_languages_final
game_time_languages_test_tidy <- tidy(game_time_languages_test)
game_time_languages_test_tidy
```

    ## # A tibble: 1 × 10
    ##   estimate estimate1 estimate2 statistic  p.value param…¹ conf.…² conf.…³ method
    ##      <dbl>     <dbl>     <dbl>     <dbl>    <dbl>   <dbl>   <dbl>   <dbl> <chr> 
    ## 1    2000.      2003      2.88     1032. 4.12e-96    43.0   1996.   2004. Welch…
    ## # … with 1 more variable: alternative <chr>, and abbreviated variable names
    ## #   ¹​parameter, ²​conf.low, ³​conf.high

I choose to produce something relevant to my t-test output by tidy()
function. By using tidy() function, I obtain the estimated mean of
release_year, the estimated mean of mean_languages_number, statistic, p
value, and etc. Based on the t.test result above, we can conclude that
the mean number of languages in games released each year has a
correlation with the release year.

<!----------------------------------------------------------------------------->

# Task 4: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 1 (Task 4.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

``` r
#how does game's price affect game's review?

game_price_classification <- within(subset_steam_games, {   
  original_price_level <- NA # initialize the variable
  original_price_level[original_price < 5] <- "Very Low" # if original_price < 5, then Very Low
  original_price_level[original_price >= 5 & original_price < 10] <- "Low" # if original_price >= 5 & original_price < 10, then Low
  original_price_level[original_price >= 10 & original_price < 25] <- "Medium" # if original_price >= 10 & original_price < 25, then Medium
  original_price_level[original_price >= 25 & original_price < 60] <- "High" # if original_price >= 25 & original_price < 60, then High
  original_price_level[original_price >= 60] <- "Very High" # if original_price >= 60, then Very High
  } )

rating_positive_and_negative = c("Positive", "Overwhelmingly Positive", "Very Positive", "Mostly Positive", "Mixed", "Mostly Negative", "Very Negative", "Overwhelmingly Negative", "Negative") # initialize the rating labels

game_price_review_summary <- game_price_classification %>%
  filter(all_rating %in% rating_positive_and_negative) %>% # remove wrong rating like user reviews and NaN 
  group_by(original_price_level) %>% # group by original_price_level
  count(all_rating) %>% # count different kinds of ratings within each group
  drop_na(original_price_level) # drop NA in original_price_level

game_price_review_summary
```

    ## # A tibble: 42 × 3
    ## # Groups:   original_price_level [5]
    ##    original_price_level all_rating                  n
    ##    <chr>                <chr>                   <int>
    ##  1 High                 Mixed                     351
    ##  2 High                 Mostly Negative            42
    ##  3 High                 Mostly Positive           259
    ##  4 High                 Negative                    2
    ##  5 High                 Overwhelmingly Positive    25
    ##  6 High                 Positive                  119
    ##  7 High                 Very Negative               3
    ##  8 High                 Very Positive             358
    ##  9 Low                  Mixed                     972
    ## 10 Low                  Mostly Negative           155
    ## # … with 32 more rows

``` r
here::here()
```

    ## [1] "/Users/vickyzhao/Desktop/stat/Ziwei_Zhao_Mini_Data_Analysis"

``` r
dir.create(here::here("output"))
write_csv(game_price_review_summary, here::here("output", "mini_project_2_price_review.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Task 3 to an R binary file (an RDS), and
load it again. Be sure to save the binary file in your `output` folder.
Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

``` r
saveRDS(game_time_languages_test_tidy, here::here("output", "mini_project_2_time_languages.rds"))
game_time_languages_test_read <- readRDS(here::here("output", "mini_project_2_time_languages.rds"))
game_time_languages_test_read
```

    ## # A tibble: 1 × 10
    ##   estimate estimate1 estimate2 statistic  p.value param…¹ conf.…² conf.…³ method
    ##      <dbl>     <dbl>     <dbl>     <dbl>    <dbl>   <dbl>   <dbl>   <dbl> <chr> 
    ## 1    2000.      2003      2.88     1032. 4.12e-96    43.0   1996.   2004. Welch…
    ## # … with 1 more variable: alternative <chr>, and abbreviated variable names
    ## #   ¹​parameter, ²​conf.low, ³​conf.high

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least three folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Task 4 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 1 document knits error-free, and the Milestone 2 document
knits error-free.

## Tagged release (1 point)

You’ve tagged a release for Milestone 1, and you’ve tagged a release for
Milestone 2.

### Attribution

Thanks to Victor Yuan for mostly putting this together.
