Analysis_LoL
================
Marshall Gallt
2/28/2022

# Introduction

League of Legends is a popular PC game. Teams all around the world
compete with the hopes of making it to Worlds. Riot has released data
for each player of each professional game of the 2022 season thus far.
In this project I will perform logistic regression in R on the
`2022 Match Data` found on Oracle Elixir’s [downloads
page](https://oracleselixir.com/tools/downloads).
`2022_LoL_esports_match_data_from_OraclesElixir_20220228.csv`

# Research Questions

In this logistic regression a number of questions could be posed.
Examples include, what indicates a winning score for each role, when is
gold income the most important, what objectives are the most decisive,
how do the teams and leagues differentiate in strategy.

# Data

The data we have available is quite large and detailed. So I will be
reducing the data to key variables and LCS matches.

``` r
Lol_match_data_2022
```

    ## # A tibble: 30,528 x 123
    ##    gameid datacompleteness url   league  year split playoffs date               
    ##    <chr>  <chr>            <chr> <chr>  <dbl> <chr>    <dbl> <dttm>             
    ##  1 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  2 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  3 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  4 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  5 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  6 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  7 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  8 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ##  9 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ## 10 ESPOR~ complete         <NA>  LCK CL  2022 Spri~        0 2022-01-10 07:44:08
    ## # ... with 30,518 more rows, and 115 more variables: game <dbl>, patch <dbl>,
    ## #   participantid <dbl>, side <chr>, position <chr>, playername <chr>,
    ## #   playerid <chr>, teamname <chr>, teamid <chr>, champion <chr>, ban1 <chr>,
    ## #   ban2 <chr>, ban3 <chr>, ban4 <chr>, ban5 <chr>, gamelength <dbl>,
    ## #   result <dbl>, kills <dbl>, deaths <dbl>, assists <dbl>, teamkills <dbl>,
    ## #   teamdeaths <dbl>, doublekills <dbl>, triplekills <dbl>, quadrakills <dbl>,
    ## #   pentakills <dbl>, firstblood <dbl>, firstbloodkill <dbl>, ...

``` r
LCS_matches <- Lol_match_data_2022 %>%
  filter(league == "LCS",
         position == "team") %>%
  subset(select = c(#'gameid',
                    'side',
                    'gamelength',
                    'result',
                    'firstblood',
                    'team kpm',
                    'firstdragon',
                    'dragons',
                    'firstbaron',
                    'barons',
                    'firsttower',
                    'towers',
                    'firstmidtower',
                    'firsttothreetowers',
                    'inhibitors',
                    'vspm',
                    'earned gpm',
                    'cspm'))

LCS_matches
```

    ## # A tibble: 168 x 17
    ##    side  gamelength result firstblood `team kpm` firstdragon dragons firstbaron
    ##    <chr>      <dbl>  <dbl>      <dbl>      <dbl> <lgl>         <dbl> <lgl>     
    ##  1 Blue        1595      0          1     0.0376 FALSE             0 FALSE     
    ##  2 Red         1595      1          0     0.564  TRUE              4 TRUE      
    ##  3 Blue        2079      1          1     0.548  TRUE              4 TRUE      
    ##  4 Red         2079      0          0     0.260  FALSE             0 FALSE     
    ##  5 Blue        3007      0          0     0.299  TRUE              3 FALSE     
    ##  6 Red         3007      1          1     0.419  FALSE             4 TRUE      
    ##  7 Blue        1976      1          0     0.668  FALSE             2 TRUE      
    ##  8 Red         1976      0          1     0.334  TRUE              2 FALSE     
    ##  9 Blue        2149      1          0     0.475  TRUE              5 TRUE      
    ## 10 Red         2149      0          1     0.335  FALSE             0 FALSE     
    ## # ... with 158 more rows, and 9 more variables: barons <dbl>, firsttower <lgl>,
    ## #   towers <dbl>, firstmidtower <lgl>, firsttothreetowers <lgl>,
    ## #   inhibitors <dbl>, vspm <dbl>, earned gpm <dbl>, cspm <dbl>

| Variable           | Type        | Description                                    |
|--------------------|-------------|------------------------------------------------|
| gameid             | Character   | Game identification (two teams)                |
| side               | Categorical | Side of the map given to team (Red or Blue)    |
| gamelength         | Numeric     | Length of game in seconds                      |
| result             | Binary      | Win = 1, lose = 0                              |
| team kpm           | Numeric     | Team’s kills per minute                        |
| firstdragon        | Binary      | True if team killed first dragon               |
| dragons            | Numeric     | Number of dragons killed by team               |
| firstbaron         | Binary      | True if team killed first baron                |
| barons             | Numeric     | Numer of dragons killed by team                |
| firsttower         | Binary      | True if team destroyed first tower             |
| towers             | Numeric     | Number of towers destroyed by team             |
| firstmidtower      | Binary      | True if team destroyed the first mid tower     |
| firsttothreetowers | Binary      | True if team was first to destroy three towers |
| inhibitors         | Numeric     | Number of inhibitors destroyed by team         |
| vspm               | Numeric     | Team’s vision score per minute                 |
| earned gpm         | Numeric     | Team’s gold earned per minute                  |
| cspm               | Numeric     | Team’s creep score per minute                  |

# Exploratory Data Analysis

First, we conduct an exploratory data analysis.

## Numerical Summaries

``` r
summary(LCS_matches)
```

    ##      side             gamelength       result      firstblood     team kpm     
    ##  Length:168         Min.   :1443   Min.   :0.0   Min.   :0.0   Min.   :0.0376  
    ##  Class :character   1st Qu.:1762   1st Qu.:0.0   1st Qu.:0.0   1st Qu.:0.2094  
    ##  Mode  :character   Median :1956   Median :0.5   Median :0.5   Median :0.3488  
    ##                     Mean   :1998   Mean   :0.5   Mean   :0.5   Mean   :0.3642  
    ##                     3rd Qu.:2212   3rd Qu.:1.0   3rd Qu.:1.0   3rd Qu.:0.4976  
    ##                     Max.   :3007   Max.   :1.0   Max.   :1.0   Max.   :0.9979  
    ##  firstdragon        dragons      firstbaron          barons      
    ##  Mode :logical   Min.   :0.000   Mode :logical   Min.   :0.0000  
    ##  FALSE:84        1st Qu.:1.000   FALSE:85        1st Qu.:0.0000  
    ##  TRUE :84        Median :2.000   TRUE :83        Median :1.0000  
    ##                  Mean   :2.321                   Mean   :0.6726  
    ##                  3rd Qu.:4.000                   3rd Qu.:1.0000  
    ##                  Max.   :5.000                   Max.   :3.0000  
    ##  firsttower          towers       firstmidtower   firsttothreetowers
    ##  Mode :logical   Min.   : 0.000   Mode :logical   Mode :logical     
    ##  FALSE:84        1st Qu.: 2.000   FALSE:84        FALSE:84          
    ##  TRUE :84        Median : 7.000   TRUE :84        TRUE :84          
    ##                  Mean   : 6.238                                     
    ##                  3rd Qu.:10.000                                     
    ##                  Max.   :11.000                                     
    ##    inhibitors        vspm          earned gpm          cspm      
    ##  Min.   :0.00   Min.   : 4.283   Min.   : 775.1   Min.   :25.32  
    ##  1st Qu.:0.00   1st Qu.: 6.851   1st Qu.: 977.6   1st Qu.:31.72  
    ##  Median :1.00   Median : 7.643   Median :1122.5   Median :33.60  
    ##  Mean   :1.06   Mean   : 7.571   Mean   :1137.9   Mean   :34.00  
    ##  3rd Qu.:2.00   3rd Qu.: 8.262   3rd Qu.:1289.1   3rd Qu.:35.56  
    ##  Max.   :6.00   Max.   :10.363   Max.   :1467.6   Max.   :46.13

## Graphical Summaries

### 

``` r
LCS_matches %>%
  data.matrix() %>%
  cor()%>%
  corrplot(is.corr = FALSE, order = 'FPC')
```

![](Analysis_LoL_files/figure-gfm/summary-tables-1-1.png)<!-- -->

# Data Cleaning

# Data Analysis

# Hypotheses

# Modeling

# Conclusion
