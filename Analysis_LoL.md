Analysis_LoL
================
Marshall Gallt
2/28/2022

In this project I wish to explore possible indicators of a winning
strategy. This will be observed as a whole and as groups by leagues,
teams, and players. I then wish to use these indicators to compare teams
and hypothesis the best team.

In this project I will be working with the
`2022_LoL_esports_match_data_from_OraclesElixir_20220228` data provided
by RIOT. The first thing needed to do is understand the way our data is
constructed. I will do this by displaying a tibble of the data and a
simple summary.

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
summary(Lol_match_data_2022)
```

    ##     gameid          datacompleteness       url               league         
    ##  Length:30528       Length:30528       Length:30528       Length:30528      
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##       year         split              playoffs      
    ##  Min.   :2022   Length:30528       Min.   :0.00000  
    ##  1st Qu.:2022   Class :character   1st Qu.:0.00000  
    ##  Median :2022   Mode  :character   Median :0.00000  
    ##  Mean   :2022                      Mean   :0.01533  
    ##  3rd Qu.:2022                      3rd Qu.:0.00000  
    ##  Max.   :2022                      Max.   :1.00000  
    ##                                                     
    ##       date                          game           patch       participantid   
    ##  Min.   :2022-01-10 07:44:08   Min.   :1.000   Min.   :12.01   Min.   :  1.00  
    ##  1st Qu.:2022-01-25 19:32:30   1st Qu.:1.000   1st Qu.:12.01   1st Qu.:  3.75  
    ##  Median :2022-02-09 02:00:55   Median :1.000   Median :12.02   Median :  6.50  
    ##  Mean   :2022-02-06 08:41:45   Mean   :1.184   Mean   :12.02   Mean   : 29.58  
    ##  3rd Qu.:2022-02-18 04:48:32   3rd Qu.:1.000   3rd Qu.:12.03   3rd Qu.:  9.25  
    ##  Max.   :2022-02-28 08:17:23   Max.   :4.000   Max.   :12.04   Max.   :200.00  
    ##                                                NA's   :24                      
    ##      side             position          playername          playerid        
    ##  Length:30528       Length:30528       Length:30528       Length:30528      
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##    teamname            teamid            champion             ban1          
    ##  Length:30528       Length:30528       Length:30528       Length:30528      
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##      ban2               ban3               ban4               ban5          
    ##  Length:30528       Length:30528       Length:30528       Length:30528      
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##    gamelength       result        kills            deaths         assists      
    ##  Min.   : 938   Min.   :0.0   Min.   : 0.000   Min.   : 0.00   Min.   :  0.00  
    ##  1st Qu.:1681   1st Qu.:0.0   1st Qu.: 1.000   1st Qu.: 2.00   1st Qu.:  3.00  
    ##  Median :1883   Median :0.5   Median : 3.000   Median : 3.00   Median :  7.00  
    ##  Mean   :1920   Mean   :0.5   Mean   : 4.661   Mean   : 4.67   Mean   : 10.34  
    ##  3rd Qu.:2119   3rd Qu.:1.0   3rd Qu.: 6.000   3rd Qu.: 5.00   3rd Qu.: 11.00  
    ##  Max.   :3577   Max.   :1.0   Max.   :46.000   Max.   :46.00   Max.   :113.00  
    ##                                                                                
    ##    teamkills       teamdeaths     doublekills    triplekills     quadrakills   
    ##  Min.   : 0.00   Min.   : 0.00   Min.   :0.00   Min.   :0.000   Min.   :0.000  
    ##  1st Qu.: 8.00   1st Qu.: 8.00   1st Qu.:0.00   1st Qu.:0.000   1st Qu.:0.000  
    ##  Median :14.00   Median :14.00   Median :0.00   Median :0.000   Median :0.000  
    ##  Mean   :13.98   Mean   :14.01   Mean   :0.54   Mean   :0.095   Mean   :0.015  
    ##  3rd Qu.:19.00   3rd Qu.:19.00   3rd Qu.:1.00   3rd Qu.:0.000   3rd Qu.:0.000  
    ##  Max.   :46.00   Max.   :46.00   Max.   :9.00   Max.   :4.000   Max.   :2.000  
    ##                                  NA's   :3768   NA's   :3768    NA's   :3768   
    ##    pentakills      firstblood     firstbloodkill firstbloodassist
    ##  Min.   :0.000   Min.   :0.0000   Min.   :0.0    Min.   :0.000   
    ##  1st Qu.:0.000   1st Qu.:0.0000   1st Qu.:0.0    1st Qu.:0.000   
    ##  Median :0.000   Median :0.0000   Median :0.0    Median :0.000   
    ##  Mean   :0.003   Mean   :0.2897   Mean   :0.1    Mean   :0.142   
    ##  3rd Qu.:0.000   3rd Qu.:1.0000   3rd Qu.:0.0    3rd Qu.:0.000   
    ##  Max.   :1.000   Max.   :1.0000   Max.   :1.0    Max.   :1.000   
    ##  NA's   :3768    NA's   :3140     NA's   :5088   NA's   :8228    
    ##  firstbloodvictim    team kpm           ckpm        firstdragon    
    ##  Min.   :0.0      Min.   :0.0000   Min.   :0.2022   Mode :logical  
    ##  1st Qu.:0.0      1st Qu.:0.2607   1st Qu.:0.6962   FALSE:2230     
    ##  Median :0.0      Median :0.4147   Median :0.8540   TRUE :2230     
    ##  Mean   :0.1      Mean   :0.4428   Mean   :0.8856   NA's :26068    
    ##  3rd Qu.:0.0      3rd Qu.:0.5949   3rd Qu.:1.0445                  
    ##  Max.   :1.0      Max.   :1.6875   Max.   :2.1083                  
    ##  NA's   :8228                                                      
    ##     dragons       opp_dragons    elementaldrakes opp_elementaldrakes
    ##  Min.   :0.000   Min.   :0.000   Mode :logical   Mode :logical      
    ##  1st Qu.:1.000   1st Qu.:1.000   FALSE:584       FALSE:584          
    ##  Median :2.000   Median :2.000   TRUE :803       TRUE :803          
    ##  Mean   :2.296   Mean   :2.296   NA's :29141     NA's :29141        
    ##  3rd Qu.:3.000   3rd Qu.:3.000                                      
    ##  Max.   :6.000   Max.   :6.000                                      
    ##  NA's   :25440   NA's   :25440                                      
    ##  infernals       mountains         clouds          oceans       
    ##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
    ##  FALSE:3004      FALSE:3045      FALSE:3009      FALSE:3008     
    ##  TRUE :1093      TRUE :1078      TRUE :1077      TRUE :1074     
    ##  NA's :26431     NA's :26405     NA's :26442     NA's :26446    
    ##                                                                 
    ##                                                                 
    ##                                                                 
    ##  chemtechs        hextechs       dragons (type unknown)   elders       
    ##  Mode :logical   Mode :logical   Min.   :0.000          Mode :logical  
    ##  FALSE:4051      FALSE:3068      1st Qu.:1.000          FALSE:4187     
    ##  TRUE :315       TRUE :1048      Median :2.000          TRUE :257      
    ##  NA's :26162     NA's :26412     Mean   :2.244          NA's :26084    
    ##                                  3rd Qu.:3.000                         
    ##                                  Max.   :5.000                         
    ##                                  NA's   :29716                         
    ##  opp_elders      firstherald      heralds        opp_heralds    
    ##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
    ##  FALSE:4187      FALSE:2231      FALSE:1426      FALSE:1426     
    ##  TRUE :257       TRUE :2229      TRUE :1679      TRUE :1679     
    ##  NA's :26084     NA's :26068     NA's :27423     NA's :27423    
    ##                                                                 
    ##                                                                 
    ##                                                                 
    ##  firstbaron          barons        opp_barons    firsttower     
    ##  Mode :logical   Min.   :0.000   Min.   :0.000   Mode :logical  
    ##  FALSE:2347      1st Qu.:0.000   1st Qu.:0.000   FALSE:2230     
    ##  TRUE :2113      Median :0.000   Median :0.000   TRUE :2230     
    ##  NA's :26068     Mean   :0.239   Mean   :0.239   NA's :26068    
    ##                  3rd Qu.:0.000   3rd Qu.:0.000                  
    ##                  Max.   :4.000   Max.   :4.000                  
    ##                  NA's   :4070    NA's   :4070                   
    ##      towers         opp_towers     firstmidtower   firsttothreetowers
    ##  Min.   : 0.000   Min.   : 0.000   Mode :logical   Mode :logical     
    ##  1st Qu.: 3.000   1st Qu.: 3.000   FALSE:2230      FALSE:2230        
    ##  Median : 7.000   Median : 7.000   TRUE :2230      TRUE :2230        
    ##  Mean   : 6.144   Mean   : 6.144   NA's :26068     NA's :26068       
    ##  3rd Qu.: 9.000   3rd Qu.: 9.000                                     
    ##  Max.   :11.000   Max.   :11.000                                     
    ##  NA's   :25440    NA's   :25440                                      
    ##  turretplates    opp_turretplates   inhibitors    opp_inhibitors 
    ##  Mode :logical   Mode :logical    Min.   :0.000   Min.   :0.000  
    ##  FALSE:164       FALSE:164        1st Qu.:0.000   1st Qu.:0.000  
    ##  TRUE :362       TRUE :362        Median :0.000   Median :0.000  
    ##  NA's :30002     NA's :30002      Mean   :0.338   Mean   :0.338  
    ##                                   3rd Qu.:0.000   3rd Qu.:0.000  
    ##                                   Max.   :8.000   Max.   :8.000  
    ##                                   NA's   :4060    NA's   :4060   
    ##  damagetochampions      dpm           damageshare    damagetakenperminute
    ##  Min.   :   629    Min.   :  18.09   Min.   :0.014   Min.   :  23.85     
    ##  1st Qu.:  7438    1st Qu.: 243.97   1st Qu.:0.121   1st Qu.: 386.10     
    ##  Median : 13058    Median : 419.96   Median :0.198   Median : 573.07     
    ##  Mean   : 20904    Mean   : 646.45   Mean   :0.200   Mean   : 928.99     
    ##  3rd Qu.: 22872    3rd Qu.: 686.33   3rd Qu.:0.269   3rd Qu.: 922.98     
    ##  Max.   :197845    Max.   :4100.26   Max.   :0.700   Max.   :4789.45     
    ##                                      NA's   :5088                        
    ##  damagemitigatedperminute  wardsplaced          wpm          wardskilled    
    ##  Min.   :  15.43          Min.   :  0.00   Min.   :0.0000   Min.   :  0.00  
    ##  1st Qu.: 268.53          1st Qu.: 11.00   1st Qu.:0.3535   1st Qu.:  5.00  
    ##  Median : 499.01          Median : 15.00   Median :0.4737   Median :  9.00  
    ##  Mean   : 792.81          Mean   : 33.38   Mean   :1.0309   Mean   : 14.72  
    ##  3rd Qu.: 872.33          3rd Qu.: 46.00   3rd Qu.:1.4651   3rd Qu.: 16.00  
    ##  Max.   :7240.09          Max.   :267.00   Max.   :6.3723   Max.   :125.00  
    ##  NA's   :3768                                                               
    ##       wcpm        controlwardsbought  visionscore         vspm        
    ##  Min.   :0.0000   Min.   :  0.00     Min.   :  4.0   Min.   : 0.1614  
    ##  1st Qu.:0.1748   1st Qu.:  5.00     1st Qu.: 31.0   1st Qu.: 1.0012  
    ##  Median :0.2867   Median :  8.00     Median : 45.0   Median : 1.3602  
    ##  Mean   :0.4508   Mean   : 13.55     Mean   : 77.2   Mean   : 2.3781  
    ##  3rd Qu.:0.4749   3rd Qu.: 16.00     3rd Qu.: 82.0   3rd Qu.: 2.5349  
    ##  Max.   :3.0620   Max.   :110.00     Max.   :599.0   Max.   :11.4872  
    ##                                                                       
    ##    totalgold        earnedgold      earned gpm      earnedgoldshare
    ##  Min.   :  3455   Min.   :  925   Min.   :  37.73   Min.   :0.049  
    ##  1st Qu.:  9386   1st Qu.: 5431   1st Qu.: 181.29   1st Qu.:0.158  
    ##  Median : 12180   Median : 8000   Median : 253.71   Median :0.211  
    ##  Mean   : 19101   Mean   :12113   Mean   : 378.77   Mean   :0.200  
    ##  3rd Qu.: 15912   3rd Qu.:11262   3rd Qu.: 337.63   3rd Qu.:0.250  
    ##  Max.   :109439   Max.   :71576   Max.   :1714.41   Max.   :0.421  
    ##                                                     NA's   :5088   
    ##    goldspent           gspd           total cs      minionkills    
    ##  Min.   :  2850   Min.   :-0.533   Min.   :  1.0   Min.   :   1.0  
    ##  1st Qu.:  8775   1st Qu.:-0.105   1st Qu.:146.8   1st Qu.:  38.0  
    ##  Median : 11275   Median : 0.000   Median :219.0   Median : 223.0  
    ##  Mean   : 17683   Mean   : 0.000   Mean   :203.4   Mean   : 261.8  
    ##  3rd Qu.: 14784   3rd Qu.: 0.105   3rd Qu.:277.0   3rd Qu.: 294.0  
    ##  Max.   :101080   Max.   : 0.533   Max.   :605.0   Max.   :1646.0  
    ##                   NA's   :25440    NA's   :5088    NA's   :628     
    ##   monsterkills    monsterkillsownjungle monsterkillsenemyjungle
    ##  Min.   :  0.00   Min.   :  0.00        Min.   : 0.000         
    ##  1st Qu.:  4.00   1st Qu.:  4.00        1st Qu.: 0.000         
    ##  Median : 23.00   Median : 19.00        Median : 1.000         
    ##  Mean   : 68.09   Mean   : 47.15        Mean   : 5.938         
    ##  3rd Qu.:138.00   3rd Qu.: 92.00        3rd Qu.: 8.000         
    ##  Max.   :434.00   Max.   :250.00        Max.   :86.000         
    ##                   NA's   :26748         NA's   :26748          
    ##       cspm            goldat10         xpat10          csat10     
    ##  Min.   : 0.0259   Min.   : 1698   Min.   :  838   Min.   :  0.0  
    ##  1st Qu.: 5.3574   1st Qu.: 2985   1st Qu.: 3091   1st Qu.: 61.0  
    ##  Median : 7.8996   Median : 3314   Median : 3940   Median : 78.0  
    ##  Mean   :10.2198   Mean   : 5224   Mean   : 6077   Mean   :106.2  
    ##  3rd Qu.: 9.5768   3rd Qu.: 3769   3rd Qu.: 4831   3rd Qu.: 91.0  
    ##  Max.   :46.1296   Max.   :20743   Max.   :21344   Max.   :390.0  
    ##  NA's   :628       NA's   :3768    NA's   :3768    NA's   :3768   
    ##   opp_goldat10     opp_xpat10      opp_csat10     golddiffat10  
    ##  Min.   : 1698   Min.   :  838   Min.   :  0.0   Min.   :-7268  
    ##  1st Qu.: 2985   1st Qu.: 3091   1st Qu.: 61.0   1st Qu.: -343  
    ##  Median : 3314   Median : 3940   Median : 78.0   Median :    0  
    ##  Mean   : 5224   Mean   : 6077   Mean   :106.2   Mean   :    0  
    ##  3rd Qu.: 3769   3rd Qu.: 4831   3rd Qu.: 91.0   3rd Qu.:  343  
    ##  Max.   :20743   Max.   :21344   Max.   :390.0   Max.   : 7268  
    ##  NA's   :3768    NA's   :3768    NA's   :3768    NA's   :3768   
    ##    xpdiffat10      csdiffat10        killsat10       assistsat10    
    ##  Min.   :-4502   Min.   :-110.00   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: -311   1st Qu.:  -8.25   1st Qu.: 0.000   1st Qu.: 0.000  
    ##  Median :    0   Median :   0.00   Median : 0.000   Median : 0.000  
    ##  Mean   :    0   Mean   :   0.00   Mean   : 0.698   Mean   : 1.078  
    ##  3rd Qu.:  311   3rd Qu.:   8.25   3rd Qu.: 1.000   3rd Qu.: 1.000  
    ##  Max.   : 4502   Max.   : 110.00   Max.   :15.000   Max.   :35.000  
    ##  NA's   :3768    NA's   :3768      NA's   :3768     NA's   :3768    
    ##    deathsat10   opp_killsat10    opp_assistsat10  opp_deathsat10
    ##  Min.   : 0.0   Min.   : 0.000   Min.   : 0.000   Min.   : 0.0  
    ##  1st Qu.: 0.0   1st Qu.: 0.000   1st Qu.: 0.000   1st Qu.: 0.0  
    ##  Median : 0.0   Median : 0.000   Median : 0.000   Median : 0.0  
    ##  Mean   : 0.7   Mean   : 0.698   Mean   : 1.078   Mean   : 0.7  
    ##  3rd Qu.: 1.0   3rd Qu.: 1.000   3rd Qu.: 1.000   3rd Qu.: 1.0  
    ##  Max.   :15.0   Max.   :15.000   Max.   :35.000   Max.   :15.0  
    ##  NA's   :3768   NA's   :3768     NA's   :3768     NA's   :3768  
    ##     goldat15         xpat15          csat15       opp_goldat15  
    ##  Min.   : 2433   Min.   : 1729   Min.   :  0.0   Min.   : 2433  
    ##  1st Qu.: 4633   1st Qu.: 5166   1st Qu.: 93.0   1st Qu.: 4633  
    ##  Median : 5251   Median : 6393   Median :125.0   Median : 5251  
    ##  Mean   : 8261   Mean   : 9797   Mean   :169.2   Mean   : 8261  
    ##  3rd Qu.: 6117   3rd Qu.: 7716   3rd Qu.:146.0   3rd Qu.: 6117  
    ##  Max.   :33969   Max.   :33982   Max.   :618.0   Max.   :33969  
    ##  NA's   :3768    NA's   :3768    NA's   :3768    NA's   :3768   
    ##    opp_xpat15      opp_csat15     golddiffat15      xpdiffat15    
    ##  Min.   : 1729   Min.   :  0.0   Min.   :-13670   Min.   :-10019  
    ##  1st Qu.: 5166   1st Qu.: 93.0   1st Qu.:  -665   1st Qu.:  -528  
    ##  Median : 6393   Median :125.0   Median :     0   Median :     0  
    ##  Mean   : 9797   Mean   :169.2   Mean   :     0   Mean   :     0  
    ##  3rd Qu.: 7716   3rd Qu.:146.0   3rd Qu.:   665   3rd Qu.:   528  
    ##  Max.   :33982   Max.   :618.0   Max.   : 13670   Max.   : 10019  
    ##  NA's   :3768    NA's   :3768    NA's   :3768     NA's   :3768    
    ##    csdiffat15     killsat15       assistsat15       deathsat15    
    ##  Min.   :-179   Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: -13   1st Qu.: 0.000   1st Qu.: 0.000   1st Qu.: 0.000  
    ##  Median :   0   Median : 1.000   Median : 1.000   Median : 1.000  
    ##  Mean   :   0   Mean   : 1.296   Mean   : 2.114   Mean   : 1.299  
    ##  3rd Qu.:  13   3rd Qu.: 2.000   3rd Qu.: 3.000   3rd Qu.: 2.000  
    ##  Max.   : 179   Max.   :21.000   Max.   :47.000   Max.   :21.000  
    ##  NA's   :3768   NA's   :3768     NA's   :3768     NA's   :3768    
    ##  opp_killsat15    opp_assistsat15  opp_deathsat15  
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 0.000   1st Qu.: 0.000   1st Qu.: 0.000  
    ##  Median : 1.000   Median : 1.000   Median : 1.000  
    ##  Mean   : 1.296   Mean   : 2.114   Mean   : 1.299  
    ##  3rd Qu.: 2.000   3rd Qu.: 3.000   3rd Qu.: 2.000  
    ##  Max.   :21.000   Max.   :47.000   Max.   :21.000  
    ##  NA's   :3768     NA's   :3768     NA's   :3768

We are obviously working with a very large data frame. So we will begin
by
