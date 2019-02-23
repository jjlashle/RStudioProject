Billboard
================
Josiah Lashley
2/22/2019

R Markdown
----------

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.0       ✔ purrr   0.3.0  
    ## ✔ tibble  2.0.1       ✔ dplyr   0.8.0.1
    ## ✔ tidyr   0.8.2       ✔ stringr 1.4.0  
    ## ✔ readr   1.3.1       ✔ forcats 0.4.0

    ## ── Conflicts ───────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
# reading in the data set of billboard 2018 - 2012 top 100 
billMess <- read.csv("billy.csv")
```

Including Plots
---------------

You can also embed plots, for example:

``` r
# the year 2018 is not as nicely formatted so clearing it for use
billCleanish <- billMess %>%
  filter(year != 2018)
# do not need all the links
bill <- billCleanish %>%
  select(-video_link,-spotify_link,-spotify_id,-analysis_url)

rap <- bill %>%
  filter(broad_genre == "rap")
```

``` r
#most apps is for most appearences
mostAppsRappers <- rap %>%
  select(main_artist,genre) %>%
  count(main_artist) %>%
  arrange(-n) %>%
  top_n(5,n) %>%
  ggplot(aes(x=reorder(main_artist,-n),y=n,fill=main_artist)) + 
  geom_col() +
  ggtitle("Most Appeared Artists on Billboard top 100") +
  xlab("Number of Appearances") +
  ylab("Artist") +
  labs(fill="Artist")
mostAppsRappers
```

![](Billboard_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
# Wanted to look at the tempos between all the genres 
tempoGenre <- bill %>%
  group_by(broad_genre) %>%
  filter(broad_genre != "unknown") %>%
  mutate(tempo = as.numeric(paste(tempo))) %>%
  select(tempo,broad_genre) %>%
  filter(!is.na(tempo)) %>%
  ggplot(aes(x=reorder(broad_genre,-tempo), y=tempo,fill=broad_genre)) + geom_boxplot() +
  ggtitle("Tempos Between Each Genre") +
  ylab("Tempo (beats per min)") +
  scale_y_continuous(limits = c(75,225)) +
  xlab("Genre") +
  labs(fill = "Genre")
```

    ## Warning: NAs introduced by coercion

    ## Warning: NAs introduced by coercion

    ## Warning: NAs introduced by coercion

    ## Warning: NAs introduced by coercion

    ## Warning: NAs introduced by coercion

``` r
tempoGenre
```

    ## Warning: Removed 106 rows containing non-finite values (stat_boxplot).

![](Billboard_files/figure-markdown_github/unnamed-chunk-4-1.png)

``` r
# Do this to find the top 3 songs to do a linear model on 
popGenre <- bill %>%
  group_by(broad_genre) %>%
  count(broad_genre) %>%
  arrange(-n)

head(popGenre)
```

    ## # A tibble: 6 x 2
    ## # Groups:   broad_genre [6]
    ##   broad_genre     n
    ##   <fct>       <int>
    ## 1 rap          1779
    ## 2 r&b          1387
    ## 3 country      1259
    ## 4 pop          1259
    ## 5 rock          839
    ## 6 unknown       435

``` r
popGenreGraph <- bill %>%
  filter(broad_genre %in% c("rap","r&b","country")) %>%
  group_by(title) %>%
  select(weeks,peak_pos,broad_genre) %>%
  arrange(-weeks) %>%
  filter(weeks > 30) %>%
  ggplot(aes(x=weeks,y=peak_pos,color=broad_genre)) + 
  geom_smooth() +
  ggtitle("Smooth Linear Model of Top 3 Genres on Billboard") +
  ylab("Peak Position on Billboard") +
  xlab("Weeks on Billboard") +
  labs(color = "Genre")
```

    ## Adding missing grouping variables: `title`

``` r
popGenreGraph
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](Billboard_files/figure-markdown_github/unnamed-chunk-5-1.png)
