
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#4 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday (after Thanksgiving) to polish things.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 4: Scraping (into) the Hall of Fame

``` r
head(HallOfFame, 3)
```

    ##    playerID yearID votedBy ballots needed votes inducted category needed_note
    ## 1  cobbty01   1936   BBWAA     226    170   222        Y   Player        <NA>
    ## 2  ruthba01   1936   BBWAA     226    170   215        Y   Player        <NA>
    ## 3 wagneho01   1936   BBWAA     226    170   215        Y   Player        <NA>

![](README_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
HallOfFame %>% 
  ggplot(aes(x = yearID, fill = inducted)) +
  geom_bar() +
  xlim(c(1936, 2022))
```

    ## Warning: Removed 4 rows containing missing values or values outside the scale range
    ## (`geom_bar()`).

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
library(rvest)
```

    ## Warning: package 'rvest' was built under R version 4.3.3

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
url <- "https://www.baseball-reference.com/awards/hof_2023.shtml"
html <- read_html(url)
tables <- html_table(html)
head(tables[[1]], 3)
```

    ## # A tibble: 3 × 39
    ##   ``    ``           ``    ``    ``    ``    ``    ``    ``    ``    ``    ``   
    ##   <chr> <chr>        <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 Rk    Name         YoB   Votes %vote HOFm  HOFs  Yrs   WAR   WAR7  JAWS  Jpos 
    ## 2 1     Scott Rolen  6th   297   76.3% 99    40    17    70.1  43.6  56.9  56.3 
    ## 3 2     Todd Helton… 5th   281   72.2% 175   59    17    61.8  46.6  54.2  53.4 
    ## # ℹ 27 more variables: `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>, …

``` r
library(rvest)
library(Lahman)
library(dplyr)
library(tidyverse)
head(HallOfFame)
```

    ##    playerID yearID votedBy ballots needed votes inducted category needed_note
    ## 1  cobbty01   1936   BBWAA     226    170   222        Y   Player        <NA>
    ## 2  ruthba01   1936   BBWAA     226    170   215        Y   Player        <NA>
    ## 3 wagneho01   1936   BBWAA     226    170   215        Y   Player        <NA>
    ## 4 mathech01   1936   BBWAA     226    170   205        Y   Player        <NA>
    ## 5 johnswa01   1936   BBWAA     226    170   189        Y   Player        <NA>
    ## 6 lajoina01   1936   BBWAA     226    170   146        N   Player        <NA>

``` r
url <- 'https://www.baseball-reference.com/awards/hof_2023.shtml'

html <- read_html(url)

tables <- html %>% html_table(fill=TRUE)
tables %>% str()
```

    ## List of 2
    ##  $ : tibble [29 × 39] (S3: tbl_df/tbl/data.frame)
    ##   ..$               : chr [1:29] "Rk" "1" "2" "3" ...
    ##   ..$               : chr [1:29] "Name" "Scott Rolen" "Todd Helton HOF" "Billy Wagner" ...
    ##   ..$               : chr [1:29] "YoB" "6th" "5th" "8th" ...
    ##   ..$               : chr [1:29] "Votes" "297" "281" "265" ...
    ##   ..$               : chr [1:29] "%vote" "76.3%" "72.2%" "68.1%" ...
    ##   ..$               : chr [1:29] "HOFm" "99" "175" "107" ...
    ##   ..$               : chr [1:29] "HOFs" "40" "59" "24" ...
    ##   ..$               : chr [1:29] "Yrs" "17" "17" "16" ...
    ##   ..$               : chr [1:29] "WAR" "70.1" "61.8" "27.7" ...
    ##   ..$               : chr [1:29] "WAR7" "43.6" "46.6" "19.8" ...
    ##   ..$               : chr [1:29] "JAWS" "56.9" "54.2" "23.7" ...
    ##   ..$               : chr [1:29] "Jpos" "56.3" "53.4" "32.5" ...
    ##   ..$ Batting Stats : chr [1:29] "G" "2038" "2247" "810" ...
    ##   ..$ Batting Stats : chr [1:29] "AB" "7398" "7962" "20" ...
    ##   ..$ Batting Stats : chr [1:29] "R" "1211" "1401" "1" ...
    ##   ..$ Batting Stats : chr [1:29] "H" "2077" "2519" "2" ...
    ##   ..$ Batting Stats : chr [1:29] "HR" "316" "369" "0" ...
    ##   ..$ Batting Stats : chr [1:29] "RBI" "1287" "1406" "1" ...
    ##   ..$ Batting Stats : chr [1:29] "SB" "118" "37" "0" ...
    ##   ..$ Batting Stats : chr [1:29] "BB" "899" "1335" "1" ...
    ##   ..$ Batting Stats : chr [1:29] "BA" ".281" ".316" ".100" ...
    ##   ..$ Batting Stats : chr [1:29] "OBP" ".364" ".414" ".143" ...
    ##   ..$ Batting Stats : chr [1:29] "SLG" ".490" ".539" ".100" ...
    ##   ..$ Batting Stats : chr [1:29] "OPS" ".855" ".953" ".243" ...
    ##   ..$ Batting Stats : chr [1:29] "OPS+" "122" "133" "-35" ...
    ##   ..$ Pitching Stats: chr [1:29] "W" "" "" "47" ...
    ##   ..$ Pitching Stats: chr [1:29] "L" "" "" "40" ...
    ##   ..$ Pitching Stats: chr [1:29] "ERA" "" "" "2.31" ...
    ##   ..$ Pitching Stats: chr [1:29] "ERA+" "" "" "187" ...
    ##   ..$ Pitching Stats: chr [1:29] "WHIP" "" "" "0.998" ...
    ##   ..$ Pitching Stats: chr [1:29] "G" "" "" "853" ...
    ##   ..$ Pitching Stats: chr [1:29] "GS" "" "" "0" ...
    ##   ..$ Pitching Stats: chr [1:29] "SV" "" "" "422" ...
    ##   ..$ Pitching Stats: chr [1:29] "IP" "" "" "903.0" ...
    ##   ..$ Pitching Stats: chr [1:29] "H" "" "" "601" ...
    ##   ..$ Pitching Stats: chr [1:29] "HR" "" "" "82" ...
    ##   ..$ Pitching Stats: chr [1:29] "BB" "" "" "300" ...
    ##   ..$ Pitching Stats: chr [1:29] "SO" "" "" "1196" ...
    ##   ..$               : chr [1:29] "Pos Summary" "*5/H" "*3H/7D9" "*1" ...
    ##  $ : tibble [2 × 40] (S3: tbl_df/tbl/data.frame)
    ##   ..$               : chr [1:2] "Rk" "1"
    ##   ..$               : chr [1:2] "Name" "Fred McGriff"
    ##   ..$               : chr [1:2] "Inducted As" "as Player"
    ##   ..$               : chr [1:2] "HOFm" "100"
    ##   ..$               : chr [1:2] "HOFs" "48"
    ##   ..$               : chr [1:2] "Yrs" "19"
    ##   ..$               : chr [1:2] "WAR" "52.6"
    ##   ..$               : chr [1:2] "WAR7" "36.0"
    ##   ..$               : chr [1:2] "JAWS" "44.3"
    ##   ..$               : chr [1:2] "Jpos" "53.4"
    ##   ..$ Batting Stats : chr [1:2] "G" "2460"
    ##   ..$ Batting Stats : chr [1:2] "AB" "8757"
    ##   ..$ Batting Stats : chr [1:2] "R" "1349"
    ##   ..$ Batting Stats : chr [1:2] "H" "2490"
    ##   ..$ Batting Stats : chr [1:2] "HR" "493"
    ##   ..$ Batting Stats : chr [1:2] "RBI" "1550"
    ##   ..$ Batting Stats : chr [1:2] "SB" "72"
    ##   ..$ Batting Stats : chr [1:2] "BB" "1305"
    ##   ..$ Batting Stats : chr [1:2] "BA" ".284"
    ##   ..$ Batting Stats : chr [1:2] "OBP" ".377"
    ##   ..$ Batting Stats : chr [1:2] "SLG" ".509"
    ##   ..$ Batting Stats : chr [1:2] "OPS" ".886"
    ##   ..$ Batting Stats : chr [1:2] "OPS+" "134"
    ##   ..$ Pitching Stats: chr [1:2] "W" ""
    ##   ..$ Pitching Stats: chr [1:2] "L" ""
    ##   ..$ Pitching Stats: chr [1:2] "ERA" ""
    ##   ..$ Pitching Stats: chr [1:2] "ERA+" ""
    ##   ..$ Pitching Stats: chr [1:2] "WHIP" ""
    ##   ..$ Pitching Stats: chr [1:2] "G" ""
    ##   ..$ Pitching Stats: chr [1:2] "GS" ""
    ##   ..$ Pitching Stats: chr [1:2] "SV" ""
    ##   ..$ Pitching Stats: chr [1:2] "IP" ""
    ##   ..$ Pitching Stats: chr [1:2] "H" ""
    ##   ..$ Pitching Stats: chr [1:2] "HR" ""
    ##   ..$ Pitching Stats: chr [1:2] "BB" ""
    ##   ..$ Pitching Stats: chr [1:2] "SO" ""
    ##   ..$               : chr [1:2] "Pos Summary" "*3DH"
    ##   ..$ Manager       : chr [1:2] "W" ""
    ##   ..$ Manager       : chr [1:2] "L" ""
    ##   ..$ Manager       : chr [1:2] "W-L%" ""

``` r
scraped_data <- tables[[1]]  # Adjust the index if needed


colnames(scraped_data) <- c("playerID", "yearID", "votedBy", "ballots", "needed", "votes", "inducted", "category", "needed_note")
scraped_data=scraped_data[-1,]

scraped_data$votes=scraped_data$ballots
scraped_data$playerID=scraped_data$yearID
scraped_data$yearID=2023
scraped_data$category='Player'
scraped_data$needed_note=NA
scraped_data$votedBy='BBWAA'
scraped_data$ballots=389
scraped_data$needed=292
scraped_data$inducted <- ifelse(scraped_data$votes > scraped_data$needed, "Y", "N")

scraped_data <- scraped_data[, 1:9]



scraped_data <- scraped_data %>%
  mutate(playerID = str_replace(playerID, "^X-", ""))

scraped_data$playerID[2] <- "Todd Helton"
scraped_data$playerID[6] <- "Carlos Beltran"
scraped_data$playerID[15] <- "Francisco Rodriguez"



scraped_data
```

    ## # A tibble: 28 × 9
    ##    playerID    yearID votedBy ballots needed votes inducted category needed_note
    ##    <chr>        <dbl> <chr>     <dbl>  <dbl> <chr> <chr>    <chr>    <lgl>      
    ##  1 Scott Rolen   2023 BBWAA       389    292 297   Y        Player   NA         
    ##  2 Todd Helton   2023 BBWAA       389    292 281   N        Player   NA         
    ##  3 Billy Wagn…   2023 BBWAA       389    292 265   N        Player   NA         
    ##  4 Andruw Jon…   2023 BBWAA       389    292 226   N        Player   NA         
    ##  5 Gary Sheff…   2023 BBWAA       389    292 214   N        Player   NA         
    ##  6 Carlos Bel…   2023 BBWAA       389    292 181   N        Player   NA         
    ##  7 Jeff Kent     2023 BBWAA       389    292 181   N        Player   NA         
    ##  8 Alex Rodri…   2023 BBWAA       389    292 139   N        Player   NA         
    ##  9 Manny Rami…   2023 BBWAA       389    292 129   N        Player   NA         
    ## 10 Omar Vizqu…   2023 BBWAA       389    292 76    Y        Player   NA         
    ## # ℹ 18 more rows

``` r
people_data <- Lahman::People %>%
  mutate(full_name = paste(nameFirst, nameLast, sep = " "))

matched_data <- left_join(scraped_data, people_data, by = c("playerID" = "full_name")) %>%
  select(playerID.y)  

colnames(matched_data) <- "playerID"
matched_data=matched_data[-15,]
scraped_data$playerID=matched_data$playerID
scraped_data$playerID[21]='dickera01'
scraped_data$playerID[23]='hardyjj01'

scraped_data
```

    ## # A tibble: 28 × 9
    ##    playerID  yearID votedBy ballots needed votes inducted category needed_note
    ##    <chr>      <dbl> <chr>     <dbl>  <dbl> <chr> <chr>    <chr>    <lgl>      
    ##  1 rolensc01   2023 BBWAA       389    292 297   Y        Player   NA         
    ##  2 heltoto01   2023 BBWAA       389    292 281   N        Player   NA         
    ##  3 wagnebi02   2023 BBWAA       389    292 265   N        Player   NA         
    ##  4 jonesan01   2023 BBWAA       389    292 226   N        Player   NA         
    ##  5 sheffga01   2023 BBWAA       389    292 214   N        Player   NA         
    ##  6 beltrca01   2023 BBWAA       389    292 181   N        Player   NA         
    ##  7 kentje01    2023 BBWAA       389    292 181   N        Player   NA         
    ##  8 rodrial01   2023 BBWAA       389    292 139   N        Player   NA         
    ##  9 ramirma02   2023 BBWAA       389    292 129   N        Player   NA         
    ## 10 vizquom01   2023 BBWAA       389    292 76    Y        Player   NA         
    ## # ℹ 18 more rows

``` r
HallOfFame <- rbind(HallOfFame, scraped_data)
tail(HallOfFame)
```

    ##       playerID yearID votedBy ballots needed votes inducted category
    ## 4346 hardyjj01   2023   BBWAA     389    292     0        N   Player
    ## 4347 ethiean01   2023   BBWAA     389    292     0        N   Player
    ## 4348 ellsbja01   2023   BBWAA     389    292     0        N   Player
    ## 4349  cainma01   2023   BBWAA     389    292     0        N   Player
    ## 4350 weaveje02   2023   BBWAA     389    292     0        N   Player
    ## 4351 werthja01   2023   BBWAA     389    292     0        N   Player
    ##      needed_note
    ## 4346        <NA>
    ## 4347        <NA>
    ## 4348        <NA>
    ## 4349        <NA>
    ## 4350        <NA>
    ## 4351        <NA>

``` r
HallOfFame %>% 
  ggplot(aes(x = yearID, fill = inducted)) +
  geom_bar() +
  xlim(c(1936, 2023))
```

    ## Warning: Removed 4 rows containing missing values or values outside the scale range
    ## (`geom_bar()`).

![](README_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
write.csv(HallOfFame, "HallOfFame.csv", row.names=FALSE)
backin <- readr::read_csv("HallOfFame.csv",  show_col_types =FALSE)
tail(backin,3)
```

    ## # A tibble: 3 × 9
    ##   playerID  yearID votedBy ballots needed votes inducted category needed_note
    ##   <chr>      <dbl> <chr>     <dbl>  <dbl> <dbl> <chr>    <chr>    <chr>      
    ## 1 cainma01    2023 BBWAA       389    292     0 N        Player   <NA>       
    ## 2 weaveje02   2023 BBWAA       389    292     0 N        Player   <NA>       
    ## 3 werthja01   2023 BBWAA       389    292     0 N        Player   <NA>
