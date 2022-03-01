Activity 8
================
Sara Knight

## Data and packages

Load the entirety of the `{tidyverse}`. You will do a little work with
dates in this activity so also load `{lubridate}`. Be sure to avoid
printing out any unnecessary information and give the code chunk a
meaningful name.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.4     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.2     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

Cheatsheets that you might want to add to your collection:

-   [`{stringr}`](https://stringr.tidyverse.org/index.html)
-   [`{forcats}`](https://forcats.tidyverse.org/)

Using `here::here`, upload the `billboard_songs.txt` file that is saved
in your `data/` folder. Notice that this file is a tab-delimited file
that is stored as a `.txt` file. Therefore, you will need to use either
`read_delim` with a `delim = ...` argument or (better) `read_tsv`.
Assign the file to a meaningful object name, be sure to avoid printing
any unnecessary information, and give the code chunk a meaningful name.

``` r
billboard <- read_tsv(here::here("data", "billboard_songs.txt"))
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   title = col_character(),
    ##   artist = col_character(),
    ##   `overall peak` = col_double(),
    ##   `weeks on chart` = col_double(),
    ##   `chart date` = col_double()
    ## )

These data include information on song popularity. In the US, the
Billboard Hot 100 is a list that comes out every week, showing the 100
most played songs that week. More information about the creation of this
dataset, as well as some analyses by the author, can be found here:
<https://mikekling.com/analyzing-the-billboard-hot-100/>. The dataset
you are provided is a limited version of the full data, containing:

-   `title`
-   `artist`
-   `overall peak`: The highest rank the song ever reached (1 is the
    best)
-   `weeks on chart`: The number of weeks the song was on the chart
-   `chart date`: The latest date the song appeared on the Billboard Hot
    100

This is a long dataset (34,605 observations)!

## Analysis

### Cleaning date

When you look at `chart date` you’ll notice that it was read in as a
numeric (`<dbl>`) variable type. What does the format of the date appear
to be in?

**Response**: YearDayMonth

In this activity, we will be using `str_...` functions to split or find
patterns in strings of information. Do the following with your dataset:

-   *Create* a variable called `date` that treats `chart date` *as a
    character* variable type,
-   Use `stringr::str_sub` on `date` to create three new columns:
    `year`, `month`, `day`
-   Replace/overwrite the variable `date` with an updated format using
    `lubridate::make_date`.

``` r
chart_date <- billboard %>% 
  pull(`chart date`)

date <- as.character(chart_date)

year <- stringr::str_sub(date, start = 1, end = 4)

month <- stringr::str_sub(date, start = 5, end = 6)

day <- stringr::str_sub(date, start = 7, end = 8)

date <- lubridate::make_date(year, month, day)
```

Once you have verified that this worked, overwrite your data object.

``` r
billboard_with_date <- billboard %>% 
  mutate(`chart date` = date)
```

#### Analyzing using dates

What 10 songs spent the longest on the charts? Give only the title,
artists, and weeks.

**Response**:

``` r
billboard_with_date %>% 
  arrange(desc(`weeks on chart`)) %>% 
  select(`title`, `artist`, `weeks on chart`) %>% 
  head(n = 10)
```

    ## # A tibble: 10 x 3
    ##    title                        artist                          `weeks on chart`
    ##    <chr>                        <chr>                                      <dbl>
    ##  1 Radioactive                  IMAGINE DRAGONS                               85
    ##  2 I'm Yours                    JASON MRAZ                                    76
    ##  3 How Do I Live                LeANN RIMES                                   69
    ##  4 Counting Stars               ONEREPUBLIC                                   68
    ##  5 Party Rock Anthem            LMFAO featuring LAUREN BENNETT…               67
    ##  6 Foolish Games / You Were Me… JEWEL                                         65
    ##  7 Rolling In The Deep          ADELE                                         64
    ##  8 Before He Cheats             CARRIE UNDERWOOD                              64
    ##  9 Ho Hey                       THE LUMINEERS                                 62
    ## 10 Need You Now                 LADY ANTEBELLUM                               60

What date did the oldest song(s) in this dataset leave the charts? Give
only the *distinct* dates.

**Response**:

``` r
billboard_with_date %>% 
  arrange(`chart date`) %>% 
  distinct(`chart date`) %>% 
  head(n = 10)
```

    ## # A tibble: 10 x 1
    ##    `chart date`
    ##    <date>      
    ##  1 1940-07-20  
    ##  2 1940-07-27  
    ##  3 1940-08-03  
    ##  4 1940-08-24  
    ##  5 1940-08-31  
    ##  6 1940-09-07  
    ##  7 1940-09-14  
    ##  8 1940-09-21  
    ##  9 1940-09-28  
    ## 10 1940-10-05

### Artists who work together

Before we get to far, let’s create common definitions:

In the string below, Nicki Minaj and Young Thug are considered to be
**featured**.

    RAE SREMMURD featuring NICKI MINAJ & YOUNG THUG

In the string below, Jessie J and Ariana Grande and Nicki Minaj are all
considered to have **collaborated** on the song.

    JESSIE J, ARIANA GRANDE & NICKI MINAJ

Which artist has been **featured** on the most Billboard charting songs?

First, create a vector object called `artist_credentials` that takes
your dataset and *then* `pull`s only the `artist` column. The `pull`
function is like `select` except that it outputs a vector rather than a
data frame (tibble). Using this function will help us avoid many
“`coercing`” warnings - I find it easier to work with vectors of
strings.

``` r
artist_credentials <- billboard_with_date %>% 
  pull(artist)
```

Using the `artist_credentials` vector, do the following. I recommend
that you do one bullet at time and verify that it worked.

-   Use `str_subset` to pick only the observations that only include
    `"featuring"`,*then*
-   Use `str_replace_all` to remove everything before and including
    “featuring” for each of these observations (i.e., replace it with
    `""`), *then*
-   Use `str_split` to separate entries with multiple featured artist -
    these could include `,`, `&`, `or` (note the whitespace), or `and`
    (note the whitespace) (this should produce a list), *then*
-   Use `unlist` to lengthen all featured artists into one vector
    (compare the output here to the output from the previous bullet
    point), *then*
-   Use `str_trim` to remove whitespace from `"both"` sides of each
    entry.

``` r
featuring <-  artist_credentials %>% 
  str_subset("featuring") %>% 
  str_replace_all(".+ featuring", "") %>% 
  str_split(",|&| or | and ") %>% 
  unlist() %>% 
  str_trim()
```

When you verify that this works, save this information into an object
called `feat_artist`.

``` r
feat_artist <- featuring
```

Now you can use this information to answer the question above.

**Response**: Lil Wayne

``` r
as.data.frame(table(feat_artist)) %>% 
  arrange(desc(Freq)) %>% 
  head(n = 1)
```

    ##   feat_artist Freq
    ## 1   LIL WAYNE   69

Which artist has **collaborated** on the most Billboard charting songs?

Using the `artist_credentials` vector you created before, do the
following:

-   Use `str_replace_all` to remove everything after and including
    “featuring” for each of these observations (i.e., replace it with
    `""`), *then*
-   Use `str_extract_all` to separate entries by `,` or `&`, *then*
-   Use `unlist` to lengthen all collaborating artists into one vector,
    *then*
-   Use `str_trim` to remove whitespace from `"both"` sides of each
    entry.

``` r
artist_collab <- artist_credentials %>% 
   str_replace_all(" featuring.+" , "") %>%
   str_replace_all(" feat.+", "") %>% 
   str_split(",|&") %>% 
   unlist() %>% 
   str_trim()
```

When you verify that this works, save this information into an object
called `collab_artist`.

``` r
collab_artist <- artist_collab
```

Now you can use this information to answer the question artist.

``` r
as.data.frame(table(collab_artist)) %>% 
  arrange(desc(Freq)) %>% 
  head(n = 1)
```

    ##   collab_artist Freq
    ## 1     GLEE CAST  206

**Response:** Glee Cast

#### Challenge 1

What songs could have been played at your 16th birthday party? That is,
which songs that eventually peaked at \#1 **entered** the charts within
a couple months (before or after) your 16th birthday? Give only the song
title, artist, and date of chart entry.

Note that this might not be possible depending on when your 16th
birthday occurred (i.e., if you turned 16 after March 7, 2015 - I need
to find more recent songs :/).

**Response**:

## Challenge 2: Data Exploration

This is somewhat unrelated to the steps you completed in the rest of
this activity, but you have added experience with new tools that you may
find useful.

Create some visual representation or summary table of these data. I
would encourage you to explore how you can use `{forcats}` to help make
your visual/table more human-readable/effective for telling the story of
your exploration.

## Attribution

Parts of this Activity are based on a lab from [Dr. Kelly
Bodwin’s](https://www.kelly-bodwin.com/) STAT 331 course.
