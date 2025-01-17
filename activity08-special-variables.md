Activity 8
================
Sara Knight

## Data and packages

Load the entirety of the `{tidyverse}`. `{forcats}` and `{stringr}` are
loaded as part of this. If you wish to work with dates during this
activity, you will need to also load `{lubridate}`. Be sure to avoid
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

-   [`{stringr}`](https://stringr.tidyverse.org/)
-   [`{forcats}`](https://forcats.tidyverse.org/)
-   [`{lubridate}`](https://lubridate.tidyverse.org/)

Using `here::here`, upload the `billboard_songs.txt` file that is saved
in your `data` folder. Notice that this file is a tab-delimited file
that is stored as a `.txt` file. Therefore, you will need to use either
`read_delim` with a `delim = ...` argument or (better) `read_tsv` .
Assign the file to a meaningful object name, be sure to avoid printing
any unnecessary information, and give the code chunk a meaningful name.

``` r
billboard <- read_tsv(here::here("data","billboard_songs.txt"))
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

This is a long dataset (34,605 observations)! You might like to create a
small dataset with only, say, 200 of the rows to try all your code out
on the smaller dataset first, and then only run the analysis of the full
data after you have experienced everything. One way to do this is to use
a function like `slice_sample(n = ...)` from `{dplyr}`.

``` r
# Randomly sample 200 rows
billboard %>% 
  slice_sample(n = 200)
```

    ## # A tibble: 200 x 5
    ##    title           artist           `overall peak` `weeks on chart` `chart date`
    ##    <chr>           <chr>                     <dbl>            <dbl>        <dbl>
    ##  1 My Hero         BLUE NOTES                   78                4     19601024
    ##  2 Lady Willpower  GARY PUCKETT AN…              2               13     19680831
    ##  3 I Never Picked… ROY CLARK                   122                1     19700612
    ##  4 The Bears       THE FASTEST GRO…            133                1     19661126
    ##  5 The Love Parade DREAM ACADEMY                36               11     19860628
    ##  6 Music, Music, … THE SENSATIONS               54                2     19610814
    ##  7 We're All In T… WEST COAST RAP …             35               16     19901006
    ##  8 To Each His Own THE PLATTERS                 21               12     19601219
    ##  9 Don't Go Away   FUN FACTORY                  93                4     19961123
    ## 10 I'll Take You … GENERAL PUBLIC               22               20     19940820
    ## # … with 190 more rows

If you wish to work with `{stringr}`, I find it useful to work on
vectors first. A way to do this is with `pull` from `{dplyr}`.

    # Turn variable from dataset into a vector
    dataset %>% 
      pull(variable)

## Analysis

You are encouraged to explore these data as you wish using functions
from the packages for the three special variable types. Some ideas that
you might be interested in:

-   What 10 songs (display title, artist, and week) were on the charts
    for the longest?

``` r
weeks <- billboard %>% 
  pull("weeks on chart")

billboard %>% 
  arrange(desc(weeks)) %>% 
  head(n = 10)
```

    ## # A tibble: 10 x 5
    ##    title           artist           `overall peak` `weeks on chart` `chart date`
    ##    <chr>           <chr>                     <dbl>            <dbl>        <dbl>
    ##  1 Radioactive     IMAGINE DRAGONS               3               85     20140510
    ##  2 I'm Yours       JASON MRAZ                    6               76     20091010
    ##  3 How Do I Live   LeANN RIMES                   2               69     19981010
    ##  4 Counting Stars  ONEREPUBLIC                   2               68     20141018
    ##  5 Party Rock Ant… LMFAO featuring…              1               67     20120721
    ##  6 Foolish Games … JEWEL                         2               65     19980221
    ##  7 Rolling In The… ADELE                         1               64     20120414
    ##  8 Before He Chea… CARRIE UNDERWOOD              8               64     20071201
    ##  9 Ho Hey          THE LUMINEERS                 3               62     20130824
    ## 10 Need You Now    LADY ANTEBELLUM               2               60     20101016

-   What distinct date did the oldest song(s) leave the charts?

``` r
last_date <- billboard %>% 
  pull("chart date")

billboard %>% 
  arrange(last_date) %>% 
  head(n = 4)
```

    ## # A tibble: 4 x 5
    ##   title       artist                `overall peak` `weeks on chart` `chart date`
    ##   <chr>       <chr>                          <dbl>            <dbl>        <dbl>
    ## 1 Playmates   KAY KYSER & HIS ORCH…              4                1     19400720
    ## 2 Where Was … CHARLIE BARNETT & HI…              6                1     19400720
    ## 3 Imagination TOMMY DORSEY & HIS O…              8                1     19400720
    ## 4 Make-Belie… MITCHELL AYERS & HIS…             10                1     19400720

-   What songs could have been played at your 16th birthday party? That
    is, which songs that eventually peaked at \#1 **entered** the charts
    within a couple months (before or after) your 16th birthday?
    Assuming you were 16 years old by 2015.

``` r
party_songs <- billboard %>% 
  pull("overall peak")


billboard %>% 
  filter(last_date <= 20061231 , last_date >= 20061201, party_songs == 1) %>% 
  select("title", "artist", "overall peak", "chart date" )
```

    ## # A tibble: 1 x 4
    ##   title         artist `overall peak` `chart date`
    ##   <chr>         <chr>           <dbl>        <dbl>
    ## 1 London Bridge FERGIE              1     20061223

-   Which artist has been **featured** on the most Billboard charting
    songs?

``` r
featured <- billboard %>% 
  pull("artist") 
  

billboard %>% 
  filter(grepl( "featuring", featured))
```

    ## # A tibble: 1,527 x 5
    ##    title       artist               `overall peak` `weeks on chart` `chart date`
    ##    <chr>       <chr>                         <dbl>            <dbl>        <dbl>
    ##  1 Uptown Fun… MARK RONSON featuri…              1               15     20150307
    ##  2 I Don't Mi… USHER featuring JUI…             13               14     20150307
    ##  3 Only        NICKI MINAJ featuri…             12               17     20150307
    ##  4 Truffle Bu… NICKI MINAJ featuri…             17                5     20150307
    ##  5 She Knows   NE-YO featuring JUI…             22               13     20150307
    ##  6 I Don't Fu… BIG SEAN featuring …             11               22     20150307
    ##  7 Somebody    NATALIE LA ROSE fea…             27                5     20150307
    ##  8 G.D.F.R.    FLO RIDA featuring …             28               12     20150307
    ##  9 Outside     CALVIN HARRIS featu…             42                8     20150307
    ## 10 Lonely Ton… BLAKE SHELTON featu…             47               12     20150307
    ## # … with 1,517 more rows

-   Which artist has **collaborated** on the most Billboard charting
    songs?
-   Create some data visualization controlling the order of the
    character/string variables.

## Attribution

Parts of this Activity are based on a lab from [Dr. Kelly
Bodwin’s](https://www.kelly-bodwin.com/) STAT 331 course.
