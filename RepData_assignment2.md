# Tornadoes are Greatest Risk to Public Health While Flooding Carries the Highest Economic Risk
Patrick J. O'Malley  
August 8, 2017  



## Synopsis

The purpose of this anlaysis is to determine which storm events in the US are most harmful with respect both to population health and economically. The initial hypothesis is that smaller but more intense events like tornadoes will have more effects on health, while larger events like hurricanes will have a greater economic effect. The data used to investigate this subject comes from the NOAA Storm Database, and contains events from the years 1950-2011. These data show that for both health and economic effects, the storm events that do the highest average damage (heat and hurricanes respectively) are overshadowed by events that have much higher number of occurances (tornadoes and floods) when looking at total damage.

## Data Processing

Begin by downloading the raw .csv file from its source on the internet


```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
download.file(url, "stormdata.csv.bz2")
```

Load the data into R


```r
storm <- read.csv("stormdata.csv.bz2")
```

View summaries of the data


```r
dim(storm) # 902,297 x 37
```

```
## [1] 902297     37
```

```r
str(storm)
```

```
## 'data.frame':	902297 obs. of  37 variables:
##  $ STATE__   : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_DATE  : Factor w/ 16335 levels "1/1/1966 0:00:00",..: 6523 6523 4242 11116 2224 2224 2260 383 3980 3980 ...
##  $ BGN_TIME  : Factor w/ 3608 levels "00:00:00 AM",..: 272 287 2705 1683 2584 3186 242 1683 3186 3186 ...
##  $ TIME_ZONE : Factor w/ 22 levels "ADT","AKS","AST",..: 7 7 7 7 7 7 7 7 7 7 ...
##  $ COUNTY    : num  97 3 57 89 43 77 9 123 125 57 ...
##  $ COUNTYNAME: Factor w/ 29601 levels "","5NM E OF MACKINAC BRIDGE TO PRESQUE ISLE LT MI",..: 13513 1873 4598 10592 4372 10094 1973 23873 24418 4598 ...
##  $ STATE     : Factor w/ 72 levels "AK","AL","AM",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ EVTYPE    : Factor w/ 985 levels "   HIGH SURF ADVISORY",..: 834 834 834 834 834 834 834 834 834 834 ...
##  $ BGN_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ BGN_AZI   : Factor w/ 35 levels "","  N"," NW",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_LOCATI: Factor w/ 54429 levels "","- 1 N Albion",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_DATE  : Factor w/ 6663 levels "","1/1/1993 0:00:00",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_TIME  : Factor w/ 3647 levels ""," 0900CST",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ COUNTY_END: num  0 0 0 0 0 0 0 0 0 0 ...
##  $ COUNTYENDN: logi  NA NA NA NA NA NA ...
##  $ END_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ END_AZI   : Factor w/ 24 levels "","E","ENE","ESE",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_LOCATI: Factor w/ 34506 levels "","- .5 NNW",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ LENGTH    : num  14 2 0.1 0 0 1.5 1.5 0 3.3 2.3 ...
##  $ WIDTH     : num  100 150 123 100 150 177 33 33 100 100 ...
##  $ F         : int  3 2 2 2 2 2 2 1 3 3 ...
##  $ MAG       : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: Factor w/ 19 levels "","-","?","+",..: 17 17 17 17 17 17 17 17 17 17 ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: Factor w/ 9 levels "","?","0","2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ WFO       : Factor w/ 542 levels ""," CI","$AC",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ STATEOFFIC: Factor w/ 250 levels "","ALABAMA, Central",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ ZONENAMES : Factor w/ 25112 levels "","                                                                                                               "| __truncated__,..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ LATITUDE  : num  3040 3042 3340 3458 3412 ...
##  $ LONGITUDE : num  8812 8755 8742 8626 8642 ...
##  $ LATITUDE_E: num  3051 0 0 0 0 ...
##  $ LONGITUDE_: num  8806 0 0 0 0 ...
##  $ REMARKS   : Factor w/ 436781 levels "","-2 at Deer Park\n",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ REFNUM    : num  1 2 3 4 5 6 7 8 9 10 ...
```

The date column is loaded as a factor so it will be converted to a date object to allow calculations


```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
storm$BGN_DATE <- as.character(storm$BGN_DATE)
storm$BGN_DATE <- mdy_hms(storm$BGN_DATE)
class(storm$BGN_DATE)
```

```
## [1] "POSIXct" "POSIXt"
```

Many events are split into serveral categories and should be combined into a single event category to get an accurate account of its overall effects.


```r
library(dplyr)
# create new variable to store combined events
storm$EVTYPE_comb <- storm$EVTYPE
# all heat related events shoul be combined
evt_heat <- droplevels(unique(storm$EVTYPE_comb[grepl("HEAT", storm$EVTYPE_comb)]))
table(droplevels(storm$EVTYPE[which(storm$EVTYPE %in% evt_heat)]))
```

```
## 
## DROUGHT/EXCESSIVE HEAT         EXCESSIVE HEAT EXCESSIVE HEAT/DROUGHT 
##                     13                   1678                      1 
##           EXTREME HEAT                   HEAT           HEAT DROUGHT 
##                     22                    767                      1 
##              HEAT WAVE      HEAT WAVE DROUGHT             HEAT WAVES 
##                     74                      1                      2 
##           HEAT/DROUGHT            RECORD HEAT       RECORD HEAT WAVE 
##                      1                     81                      1 
##  RECORD/EXCESSIVE HEAT 
##                      3
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_heat)] <- "HEAT"
table(droplevels(storm$EVTYPE_comb[grepl("HEAT", storm$EVTYPE_comb)]))
```

```
## 
## HEAT 
## 2645
```

```r
# combine other events
# hurricane
evt_hurr <- droplevels(unique(storm$EVTYPE_comb[grepl("HURRICANE", storm$EVTYPE_comb)]))
evt_hurr
```

```
## [1] HURRICANE OPAL/HIGH WINDS  HURRICANE ERIN            
## [3] HURRICANE OPAL             HURRICANE                 
## [5] HURRICANE-GENERATED SWELLS HURRICANE EMILY           
## [7] HURRICANE GORDON           HURRICANE FELIX           
## [9] HURRICANE/TYPHOON         
## 9 Levels: HURRICANE HURRICANE-GENERATED SWELLS ... HURRICANE/TYPHOON
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_hurr)] <- "HURRICANE"
table(droplevels(storm$EVTYPE_comb[grepl("HURRICANE", storm$EVTYPE_comb)]))
```

```
## 
## HURRICANE 
##       286
```

```r
# flood
evt_flood <- droplevels(unique(storm$EVTYPE_comb[grepl("FLOOD|FLD", storm$EVTYPE_comb)]))
evt_flood
```

```
##   [1] ICE STORM/FLASH FLOOD          FLASH FLOOD                   
##   [3] FLASH FLOODING                 FLOODING                      
##   [5] FLOOD                          FLASH FLOODING/THUNDERSTORM WI
##   [7] BREAKUP FLOODING               RIVER FLOOD                   
##   [9] COASTAL FLOOD                  FLOOD WATCH/                  
##  [11] FLASH FLOODS                   FLOODING/HEAVY RAIN           
##  [13] HEAVY SURF COASTAL FLOODING    URBAN FLOODING                
##  [15] URBAN/SMALL FLOODING           LOCAL FLOOD                   
##  [17] FLOOD/FLASH FLOOD              FLOOD/RAIN/WINDS              
##  [19] FLASH FLOOD WINDS              URBAN/SMALL STREAM FLOODING   
##  [21] STREAM FLOODING                FLASH FLOOD/                  
##  [23] FLOOD/RAIN/WIND                SMALL STREAM URBAN FLOOD      
##  [25] URBAN FLOOD                    HEAVY RAIN/FLOODING           
##  [27] COASTAL FLOODING               HIGH WINDS/FLOODING           
##  [29] URBAN/SMALL STREAM FLOOD       MINOR FLOODING                
##  [31] URBAN/SMALL STREAM  FLOOD      URBAN AND SMALL STREAM FLOOD  
##  [33] SMALL STREAM FLOODING          FLOODS                        
##  [35] SMALL STREAM AND URBAN FLOODIN SMALL STREAM/URBAN FLOOD      
##  [37] SMALL STREAM AND URBAN FLOOD   RURAL FLOOD                   
##  [39] THUNDERSTORM WINDS URBAN FLOOD MAJOR FLOOD                   
##  [41] ICE JAM FLOODING               STREET FLOOD                  
##  [43] SMALL STREAM FLOOD             LAKE FLOOD                    
##  [45] URBAN AND SMALL STREAM FLOODIN RIVER AND STREAM FLOOD        
##  [47] MINOR FLOOD                    HIGH WINDS/COASTAL FLOOD      
##  [49] RIVER FLOODING                 FLOOD/RIVER FLOOD             
##  [51] MUD SLIDES URBAN FLOODING      HEAVY SNOW/HIGH WINDS & FLOOD 
##  [53] HAIL FLOODING                  THUNDERSTORM WINDS/FLASH FLOOD
##  [55] HEAVY RAIN AND FLOOD           LOCAL FLASH FLOOD             
##  [57] FLOOD/FLASH FLOODING           COASTAL/TIDAL FLOOD           
##  [59] FLASH FLOOD/FLOOD              FLASH FLOOD FROM ICE JAMS     
##  [61] FLASH FLOOD - HEAVY RAIN       FLASH FLOOD/ STREET           
##  [63] FLASH FLOOD/HEAVY RAIN         HEAVY RAIN; URBAN FLOOD WINDS;
##  [65] FLOOD FLASH                    FLOOD FLOOD/FLASH             
##  [67] TIDAL FLOOD                    FLOOD/FLASH                   
##  [69] HEAVY RAINS/FLOODING           THUNDERSTORM WINDS/FLOODING   
##  [71] HIGHWAY FLOODING               FLASH FLOOD/ FLOOD            
##  [73] HEAVY RAIN/MUDSLIDES/FLOOD     BEACH EROSION/COASTAL FLOOD   
##  [75] SNOWMELT FLOODING              FLASH FLOODING/FLOOD          
##  [77] BEACH FLOOD                    THUNDERSTORM WINDS/ FLOOD     
##  [79] FLOOD & HEAVY RAIN             FLOOD/FLASHFLOOD              
##  [81] URBAN SMALL STREAM FLOOD       URBAN FLOOD LANDSLIDE         
##  [83] URBAN FLOODS                   HEAVY RAIN/URBAN FLOOD        
##  [85] FLASH FLOOD/LANDSLIDE          LANDSLIDE/URBAN FLOOD         
##  [87] FLASH FLOOD LANDSLIDES         URBAN/SML STREAM FLD          
##  [89] COASTALFLOOD                   STREET FLOODING               
##  [91] TIDAL FLOODING                  COASTAL FLOOD                
##  [93] URBAN/SML STREAM FLDG          URBAN/SMALL STRM FLDG         
##  [95] COASTAL FLOODING/EROSION       URBAN/STREET FLOODING         
##  [97] COASTAL  FLOODING/EROSION      FLOOD/FLASH/FLOOD             
##  [99]  FLASH FLOOD                   CSTL FLOODING/EROSION         
## [101] LAKESHORE FLOOD               
## 101 Levels:  COASTAL FLOOD  FLASH FLOOD ... URBAN/STREET FLOODING
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_flood)] <- "FLOOD"
table(droplevels(storm$EVTYPE_comb[grepl("FLOOD", storm$EVTYPE_comb)]))
```

```
## 
## FLOOD 
## 86059
```

```r
# wildfire
evt_fire <- droplevels(unique(storm$EVTYPE_comb[grepl("[Ff][Ii][Rr][Ee]", storm$EVTYPE_comb)]))
evt_fire
```

```
##  [1] WILD FIRES        WILDFIRE          WILD/FOREST FIRE 
##  [4] GRASS FIRES       LIGHTNING FIRE    FOREST FIRES     
##  [7] WILDFIRES         WILD/FOREST FIRES BRUSH FIRES      
## [10] BRUSH FIRE        RED FLAG FIRE WX 
## 11 Levels: BRUSH FIRE BRUSH FIRES FOREST FIRES ... WILDFIRES
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_fire)] <- "WILDFIRE"
table(droplevels(storm$EVTYPE_comb[grepl("WILDFIRE", storm$EVTYPE_comb)]))
```

```
## 
## WILDFIRE 
##     4240
```

```r
# thunderstorm
evt_thunderstorm <- droplevels(unique(storm$EVTYPE_comb[grepl("THUNDERSTORM", storm$EVTYPE_comb)]))
evt_thunderstorm
```

```
##  [1] THUNDERSTORM WINDS             THUNDERSTORM WIND             
##  [3] THUNDERSTORM WINS              THUNDERSTORM WINDS LIGHTNING  
##  [5] THUNDERSTORM WINDS/HAIL        THUNDERSTORM WINDS HAIL       
##  [7] THUNDERSTORM                   THUNDERSTORM WINDS/FUNNEL CLOU
##  [9] SEVERE THUNDERSTORM            SEVERE THUNDERSTORMS          
## [11] SEVERE THUNDERSTORM WINDS      THUNDERSTORMS WINDS           
## [13] THUNDERSTORMS                  LIGHTNING THUNDERSTORM WINDSS 
## [15] THUNDERSTORM WINDS 60          THUNDERSTORM WINDSS           
## [17] LIGHTNING THUNDERSTORM WINDS   LIGHTNING AND THUNDERSTORM WIN
## [19] THUNDERSTORM WINDS53           THUNDERSTORM WINDS 13         
## [21] THUNDERSTORM WINDS SMALL STREA THUNDERSTORM WINDS 2          
## [23] THUNDERSTORM WINDS 61          THUNDERSTORM DAMAGE           
## [25] THUNDERSTORMW 50               THUNDERSTORMS WIND            
## [27] THUNDERSTORM  WINDS            THUNDERSTORM WINDS/ HAIL      
## [29] THUNDERSTORM WIND/LIGHTNING    THUNDERSTORM WIND G50         
## [31] THUNDERSTORM WINDS/HEAVY RAIN  THUNDERSTORM WINDS      LE CEN
## [33] THUNDERSTORM WINDS G           THUNDERSTORM WIND G60         
## [35] THUNDERSTORM WINDS.            THUNDERSTORM WIND G55         
## [37] THUNDERSTORM WINDS G60         THUNDERSTORM WINDS FUNNEL CLOU
## [39] THUNDERSTORM WINDS 62          THUNDERSTORM WINDS 53         
## [41] THUNDERSTORM WIND 59           THUNDERSTORM WIND 52          
## [43] THUNDERSTORM WIND 69           THUNDERSTORMW WINDS           
## [45] THUNDERSTORM WIND 60 MPH       THUNDERSTORM WIND 65MPH       
## [47] THUNDERSTORM WIND/ TREES       THUNDERSTORM WIND/AWNING      
## [49] THUNDERSTORM WIND 98 MPH       THUNDERSTORM WIND TREES       
## [51] THUNDERSTORM WIND 59 MPH       THUNDERSTORM WINDS 63 MPH     
## [53] THUNDERSTORM WIND/ TREE        THUNDERSTORM DAMAGE TO        
## [55] THUNDERSTORM WIND 65 MPH       THUNDERSTORM WIND.            
## [57] THUNDERSTORM WIND 59 MPH.      THUNDERSTORM HAIL             
## [59] THUNDERSTORM WINDSHAIL         THUNDERSTORM WINDS AND        
## [61] THUNDERSTORM WINDS 50          THUNDERSTORM WIND G52         
## [63] THUNDERSTORM WINDS 52          THUNDERSTORM WIND G51         
## [65] THUNDERSTORM WIND G61          THUNDERSTORM W INDS           
## [67] THUNDERSTORM WIND 50           THUNDERSTORM WIND 56          
## [69] THUNDERSTORM WIND/HAIL         THUNDERSTORMW                 
## [71] THUNDERSTORMWINDS              THUNDERSTORM WINDS HEAVY RAIN 
## [73] THUNDERSTORM WIND (G40)        GUSTY THUNDERSTORM WINDS      
## [75] GUSTY THUNDERSTORM WIND        MARINE THUNDERSTORM WIND      
## 76 Levels: GUSTY THUNDERSTORM WIND ... THUNDERSTORMWINDS
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_thunderstorm)] <- "THUNDERSTORM"
table(droplevels(storm$EVTYPE_comb[grepl("THUNDERSTORM", storm$EVTYPE_comb)]))
```

```
## 
## THUNDERSTORM 
##       109565
```

```r
# wind
evt_wind <- droplevels(unique(storm$EVTYPE_comb[grepl("WIND", storm$EVTYPE_comb)]))
evt_wind
```

```
##   [1] TSTM WIND                      HIGH WINDS                    
##   [3] WIND                           HIGH WIND                     
##   [5] WIND CHILL                     HIGH WIND/BLIZZARD            
##   [7] HIGH WIND AND HIGH TIDES       HIGH WIND/BLIZZARD/FREEZING RA
##   [9] HIGH WIND AND HEAVY SNOW       RECORD COLD AND HIGH WIND     
##  [11] HIGH WINDS HEAVY RAINS         HIGH WIND/ BLIZZARD           
##  [13] BLIZZARD/HIGH WIND             HIGH WIND/LOW WIND CHILL      
##  [15] HIGH WINDS AND WIND CHILL      HEAVY SNOW/HIGH WINDS/FREEZING
##  [17] WIND CHILL/HIGH WIND           HIGH WIND/WIND CHILL/BLIZZARD 
##  [19] HIGH WIND/WIND CHILL           HIGH WIND/HEAVY SNOW          
##  [21] HIGH WIND/SEAS                 HIGH WINDS/HEAVY RAIN         
##  [23] HEAVY SNOW/WIND                WIND DAMAGE                   
##  [25] WINTER STORM/HIGH WIND         WINTER STORM/HIGH WINDS       
##  [27] GUSTY WINDS                    STRONG WINDS                  
##  [29] SNOW AND WIND                  HIGH WINDS DUST STORM         
##  [31] WINTER STORM HIGH WINDS        WINDS                         
##  [33] STRONG WIND                    HIGH WIND DAMAGE              
##  [35] DOWNBURST WINDS                DRY MICROBURST WINDS          
##  [37] DRY MIRCOBURST WINDS           MICROBURST WINDS              
##  [39] HIGH WINDS 57                  HIGH WINDS 66                 
##  [41] HIGH WINDS 76                  HIGH WINDS 63                 
##  [43] HIGH WINDS 67                  HEAVY SNOW/HIGH WINDS         
##  [45] HIGH WINDS 82                  HIGH WINDS 80                 
##  [47] HIGH WINDS 58                  HIGH WINDS 73                 
##  [49] HIGH WINDS 55                  TORNADOES, TSTM WIND, HAIL    
##  [51] HEAVY SNOW/HIGH WIND           HIGH WINDS/                   
##  [53] EXTREME WIND CHILLS            HIGH  WINDS                   
##  [55] EXTREME WIND CHILL             GRADIENT WINDS                
##  [57] BLOWING SNOW- EXTREME WIND CHI SNOW- HIGH WIND- WIND CHILL   
##  [59] TSTM WIND 51                   TSTM WIND 50                  
##  [61] TSTM WIND 52                   TSTM WIND 55                  
##  [63] THUNDERTORM WINDS              HAIL/WINDS                    
##  [65] WIND STORM                     HAIL/WIND                     
##  [67] WIND/HAIL                      TUNDERSTORM WIND              
##  [69] THUNDERTSORM WIND              THUNDESTORM WINDS             
##  [71] HIGH WIND 63                   THUNDERSTROM WINDS            
##  [73] BLIZZARD AND EXTREME WIND CHIL LOW WIND CHILL                
##  [75] BLOWING SNOW & EXTREME WIND CH DUST STORM/HIGH WINDS         
##  [77] HEAVY SNOW AND HIGH WINDS      HIGH WIND 70                  
##  [79] RAIN AND WIND                  LIGHTNING AND WINDS           
##  [81] TSTM WIND G58                  THUDERSTORM WINDS             
##  [83] STORM FORCE WINDS              TSTM WIND DAMAGE              
##  [85] RAIN/WIND                      THUNDERESTORM WINDS           
##  [87] THUNDEERSTORM WINDS            THUNERSTORM WINDS             
##  [89] HIGH WINDS/COLD                COLD/WINDS                    
##  [91] ICE/STRONG WINDS               EXTREME WIND CHILL/BLOWING SNO
##  [93] SNOW/HIGH WINDS                HIGH WINDS/SNOW               
##  [95] HEAVY SNOW AND STRONG WINDS    BLOWING SNOW/EXTREME WIND CHIL
##  [97] TSTM WINDS                     TSTM WIND 65)                 
##  [99] HIGH WIND AND SEAS             THUNDERSTROM WIND             
## [101] HIGH WIND 48                   EXTREME WINDCHILL             
## [103] TSTM WIND/HAIL                 HEAVY RAIN/WIND               
## [105] GUSTY WIND/HVY RAIN            TSTM WIND (G45)               
## [107] GUSTY WIND                     TSTM WIND 40                  
## [109] TSTM WIND 45                   TSTM WIND (41)                
## [111] TSTM WIND (G40)                 TSTM WIND                    
## [113] STRONG WIND GUST               GRADIENT WIND                 
## [115] TSTM WIND AND LIGHTNING         TSTM WIND (G45)              
## [117] TSTM WIND  (G45)               HIGH WIND (G40)               
## [119] TSTM WIND (G35)                WAKE LOW WIND                 
## [121] COLD WIND CHILL TEMPERATURES   BITTER WIND CHILL             
## [123] BITTER WIND CHILL TEMPERATURES WIND ADVISORY                 
## [125] GUSTY WIND/HAIL                EXTREME WINDCHILL TEMPERATURES
## [127] WIND AND WAVE                   WIND                         
## [129] TSTM WIND G45                  NON-SEVERE WIND DAMAGE        
## [131] WIND GUSTS                     GUSTY LAKE WIND               
## [133] NON-TSTM WIND                  NON TSTM WIND                 
## [135] MARINE TSTM WIND               WHIRLWIND                     
## [137] EXTREME COLD/WIND CHILL        COLD/WIND CHILL               
## [139] MARINE HIGH WIND               MARINE STRONG WIND            
## 140 Levels:  TSTM WIND  TSTM WIND (G45)  WIND ... WINTER STORM/HIGH WINDS
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_wind)] <- "WIND"
table(droplevels(storm$EVTYPE_comb[grepl("WIND", storm$EVTYPE_comb)]))
```

```
## 
##   WIND 
## 255358
```

```r
# snow
evt_snow <- droplevels(unique(storm$EVTYPE_comb[grepl("[Ss][Nn][Oo][Ww]", storm$EVTYPE_comb)]))
evt_snow
```

```
##   [1] SNOW                          SNOW/ICE                     
##   [3] HEAVY SNOW                    HEAVY SNOW/HIGH              
##   [5] RECORD SNOWFALL               HEAVY SNOWPACK               
##   [7] BLIZZARD/HEAVY SNOW           BLOWING SNOW                 
##   [9] LIGHT SNOW AND SLEET          FIRST SNOW                   
##  [11] SLEET/RAIN/SNOW               RAIN/SNOW                    
##  [13] SNOW/RAIN/SLEET               FREEZING RAIN/SNOW           
##  [15] THUNDERSNOW                   HEAVY RAIN/SNOW              
##  [17] SNOW/SLEET/FREEZING RAIN      EARLY SNOW                   
##  [19] HEAVY SNOW/BLOWING SNOW       SNOW AND HEAVY SNOW          
##  [21] SNOW/HEAVY SNOW               ICE/SNOW                     
##  [23] HEAVY SNOW/BLIZZARD           SNOW AND ICE                 
##  [25] SNOWSTORM                     SNOW AND ICE STORM           
##  [27] HEAVY SNOW/SLEET              HEAVY SNOW/ICE STORM         
##  [29] HEAVY SNOW AND ICE STORM      SNOW/RAIN                    
##  [31] SNOW SQUALLS                  SNOW SQUALL                  
##  [33] HEAVY LAKE SNOW               HEAVY SNOW/FREEZING RAIN     
##  [35] LAKE EFFECT SNOW              HEAVY WET SNOW               
##  [37] BLIZZARD AND HEAVY SNOW       HEAVY SNOW AND ICE           
##  [39] ICE STORM AND SNOW            HEAVY SNOW ANDBLOWING SNOW   
##  [41] HEAVY SNOW/ICE                HEAVY SNOW/WINTER STORM      
##  [43] WET SNOW                      SNOW/ICE STORM               
##  [45] LIGHT SNOW                    RECORD SNOW                  
##  [47] SNOW/COLD                     HEAVY SNOW SQUALLS           
##  [49] HEAVY SNOW/SQUALLS            HEAVY SNOW-SQUALLS           
##  [51] SNOW FREEZING RAIN            LACK OF SNOW                 
##  [53] SNOW/SLEET                    SNOW/FREEZING RAIN           
##  [55] SNOW DROUGHT                  HEAVY SNOW   FREEZING RAIN   
##  [57] ICE AND SNOW                  FREEZING RAIN AND SNOW       
##  [59] HEAVY SNOW & ICE              SNOW SHOWERS                 
##  [61] HEAVY SNOW/BLIZZARD/AVALANCHE RECORD SNOW/COLD             
##  [63] SNOW/ BITTER COLD             SNOW SLEET                   
##  [65] SNOW ACCUMULATION             SNOW/ ICE                    
##  [67] SNOW/BLOWING SNOW             NEAR RECORD SNOW             
##  [69] SLEET/SNOW                    SNOW/SLEET/RAIN              
##  [71] SNOW AND COLD                 PROLONG COLD/SNOW            
##  [73] SNOW\\COLD                    SNOWFALL RECORD              
##  [75] HEAVY SNOW AND                LAKE-EFFECT SNOW             
##  [77] Snow                          Snow Squalls                 
##  [79] Light Snow/Flurries           blowing snow                 
##  [81] Late-season Snowfall          Snow squalls                 
##  [83] Ice/Snow                      Snow Accumulation            
##  [85] Drifting Snow                 Heavy snow shower            
##  [87] LATE SNOW                     Record May Snow              
##  [89] Record Winter Snow            Light snow                   
##  [91] Late Season Snowfall          Light Snow                   
##  [93] Snow and Ice                  Light Snowfall               
##  [95] Blowing Snow                  Early snowfall               
##  [97] Monthly Snowfall              Seasonal Snowfall            
##  [99] Thundersnow shower            COLD AND SNOW                
## [101] Lake Effect Snow              Snow and sleet               
## [103] Mountain Snows                MODERATE SNOW                
## [105] MODERATE SNOWFALL             SNOW AND SLEET               
## [107] LIGHT SNOW/FREEZING PRECIP    EARLY SNOWFALL               
## [109] EXCESSIVE SNOW                MONTHLY SNOWFALL             
## [111] LATE SEASON SNOW              SNOW ADVISORY                
## [113] UNUSUALLY LATE SNOW           ACCUMULATED SNOWFALL         
## [115] FALLING SNOW/ICE             
## 115 Levels: ACCUMULATED SNOWFALL ... WET SNOW
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_snow)] <- "SNOW"
table(droplevels(storm$EVTYPE_comb[grepl("SNOW", storm$EVTYPE_comb)]))
```

```
## 
##  SNOW 
## 17677
```

```r
# hail
evt_hail <- droplevels(unique(storm$EVTYPE_comb[grepl("[Hh][Aa][Ii][Ll]", storm$EVTYPE_comb)]))
evt_hail
```

```
##  [1] HAIL              HAIL 1.75)        HAIL STORM       
##  [4] HAIL 75           SMALL HAIL        HAIL 80          
##  [7] FUNNEL CLOUD/HAIL HAIL 0.75         HAIL 1.00        
## [10] HAIL 1.75         HAIL 225          HAIL 0.88        
## [13] DEEP HAIL         HAIL 88           HAIL 175         
## [16] HAIL 100          HAIL 150          HAIL 075         
## [19] HAIL 125          HAIL 200          HAIL DAMAGE      
## [22] HAIL 088          HAIL/ICY ROADS    HAIL ALOFT       
## [25] HAIL 275          HAIL 450          HAILSTORM        
## [28] HAILSTORMS        small hail        Hail(0.75)       
## [31] Small Hail        LATE SEASON HAIL  NON SEVERE HAIL  
## [34] MARINE HAIL      
## 34 Levels: DEEP HAIL FUNNEL CLOUD/HAIL HAIL HAIL 0.75 ... SMALL HAIL
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_hail)] <- "HAIL"
table(droplevels(storm$EVTYPE_comb[grepl("HAIL", storm$EVTYPE_comb)]))
```

```
## 
##   HAIL 
## 289275
```

```r
# rip current
evt_rip <- droplevels(unique(storm$EVTYPE_comb[grepl("[Rr][Ii][Pp]", storm$EVTYPE_comb)]))
evt_rip
```

```
## [1] RIP CURRENT             RIP CURRENTS HEAVY SURF RIP CURRENTS/HEAVY SURF
## [4] RIP CURRENTS           
## 4 Levels: RIP CURRENT RIP CURRENTS ... RIP CURRENTS/HEAVY SURF
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_rip)] <- "RIP CURRENT"
table(droplevels(storm$EVTYPE_comb[grepl("RIP CURRENT", storm$EVTYPE_comb)]))
```

```
## 
## RIP CURRENT 
##         777
```

```r
# winter storm
evt_winter <- droplevels(unique(storm$EVTYPE_comb[grepl("[Ww][Ii][Nn][Tt][Ee][Rr]|ICE|BLIZZARD|SNOW", storm$EVTYPE_comb)]))
evt_winter
```

```
##  [1] SNOW                   WINTER STORM           BLIZZARD              
##  [4] BLIZZARD WEATHER       ICE STORM              WINTER STORMS         
##  [7] ICE                    WINTER WEATHER         GLAZE ICE             
## [10] SLEET/ICE STORM        GROUND BLIZZARD        ICE FLOES             
## [13] BLIZZARD/FREEZING RAIN GLAZE/ICE STORM        BLIZZARD/WINTER STORM 
## [16] ICE JAM                WINTER MIX             Winter Weather        
## [19] BLACK ICE              WINTERY MIX            ICE PELLETS           
## [22] ICE ROADS              WINTER WEATHER MIX     PATCHY ICE            
## [25] WINTER WEATHER/MIX     ICE ON ROAD           
## 26 Levels: BLACK ICE BLIZZARD BLIZZARD WEATHER ... WINTERY MIX
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_winter)] <- "WINTER STORM"
table(droplevels(storm$EVTYPE_comb[grepl("WINTER STORM", storm$EVTYPE_comb)]))
```

```
## 
## WINTER STORM 
##        42092
```

```r
# tornado
evt_tornado <- droplevels(unique(storm$EVTYPE_comb[grepl("[Ff][Uu][Nn][Nn][Ee][Ll]|TORNADO", storm$EVTYPE_comb)]))
evt_tornado
```

```
##  [1] TORNADO                 FUNNEL CLOUD           
##  [3] TORNADO F0              FUNNEL                 
##  [5] WALL CLOUD/FUNNEL CLOUD FUNNEL CLOUDS          
##  [7] TORNADOS                WATERSPOUT/TORNADO     
##  [9] WATERSPOUT TORNADO      WATERSPOUT-TORNADO     
## [11] COLD AIR FUNNEL         COLD AIR FUNNELS       
## [13] COLD AIR TORNADO        WATERSPOUT/ TORNADO    
## [15] TORNADO F3              FUNNEL CLOUD.          
## [17] TORNADO F1              TORNADO/WATERSPOUT     
## [19] TORNADO F2              TORNADOES              
## [21] FUNNELS                 WATERSPOUT FUNNEL CLOUD
## [23] Funnel Cloud            TORNADO DEBRIS         
## 24 Levels: COLD AIR FUNNEL COLD AIR FUNNELS COLD AIR TORNADO ... WATERSPOUT/TORNADO
```

```r
storm$EVTYPE_comb[which(storm$EVTYPE_comb %in% evt_tornado)] <- "TORNADO"
table(droplevels(storm$EVTYPE_comb[grepl("TORNADO", storm$EVTYPE_comb)]))
```

```
## 
## TORNADO 
##   67686
```

```r
storm %>% 
        group_by(EVTYPE_comb) %>% 
        summarise(n = n()) %>% 
        arrange(desc(n)) %>% 
        slice(1:10)
```

```
## # A tibble: 10 x 2
##     EVTYPE_comb      n
##          <fctr>  <int>
##  1         HAIL 289275
##  2         WIND 255358
##  3 THUNDERSTORM 109565
##  4        FLOOD  86059
##  5      TORNADO  67686
##  6 WINTER STORM  42092
##  7    LIGHTNING  15754
##  8   HEAVY RAIN  11723
##  9     WILDFIRE   4240
## 10   WATERSPOUT   3796
```


## Results

### Population Health

View top events with respect to harm to population health. We are interested in the FATALITIES and INJURIES columns for determining an events affect on health. Here are tables of the two column's values.


```r
# look at distributions of deaths and injuries

table(storm$FATALITIES)
```

```
## 
##      0      1      2      3      4      5      6      7      8      9 
## 895323   5010    996    314    166    114     71     53     33     30 
##     10     11     12     13     14     15     16     17     18     19 
##     30     24     12     13     12      5     11      7      2      2 
##     20     21     22     23     24     25     26     27     29     30 
##      7      3      6      3      4      5      1      3      3      3 
##     31     32     33     34     36     37     38     42     44     46 
##      3      3      3      1      1      1      1      3      1      1 
##     49     50     57     67     74     75     90     99    114    116 
##      1      1      2      1      1      1      1      1      1      1 
##    158    583 
##      1      1
```

```r
table(storm$INJURIES)
```

```
## 
##      0      1      2      3      4      5      6      7      8      9 
## 884693   7756   3134   1552    931    709    529    280    255    186 
##     10     11     12     13     14     15     16     17     18     19 
##    271    109    181     84     84    138     51     57     52     30 
##     20     21     22     23     24     25     26     27     28     29 
##    130     28     40     26     37     65     31     23     25     16 
##     30     31     32     33     34     35     36     37     38     39 
##     67     12     19     13     10     24     12     13     10      8 
##     40     41     42     43     44     45     46     47     48     49 
##     48      8     12      7      6     14      7     10      8      7 
##     50     51     52     53     54     55     56     57     58     59 
##     58      6      5      9      4     12      4      7      6      9 
##     60     61     62     63     64     65     66     67     68     69 
##     17      4      4      3      4     10      1      3      2      2 
##     70     71     72     73     74     75     76     77     78     79 
##     13      2      4      2      1     14      2      2      3      1 
##     80     81     82     83     85     87     88     89     90     91 
##     11      2      1      1      2      1      3      2      6      1 
##     92     93     94     95     96     97     98    100    101    102 
##      1      2      1      2      1      3      1     34      1      2 
##    103    104    105    106    108    109    110    111    112    115 
##      2      1      1      1      1      1      3      1      3      2 
##    116    118    119    120    121    122    123    125    129    130 
##      2      1      1      3      2      2      1      2      2      4 
##    135    136    137    138    140    142    143    144    145    150 
##      2      1      3      1      3      1      1      1      1     12 
##    152    153    154    156    159    160    165    166    170    172 
##      1      2      1      1      1      1      2      1      2      1 
##    175    176    177    180    181    185    190    192    195    200 
##      4      1      2      3      1      2      1      2      1     20 
##    207    210    215    216    223    224    225    230    234    240 
##      1      1      1      1      1      1      2      1      1      1 
##    241    246    250    252    257    258    266    270    275    280 
##      1      1      3      2      2      1      1      3      1      2 
##    293    300    306    316    325    342    350    385    397    410 
##      1      5      1      1      1      1      4      1      1      1 
##    411    437    450    463    500    504    519    550    560    597 
##      1      1      3      1      7      1      1      1      1      1 
##    600    700    750    780    785    800   1150   1228   1568   1700 
##      1      1      1      1      1      2      2      1      1      1
```

The vast majority of events result in no deaths or injuries. Since we are interseted in events that are damaging to the population health, we will filter for events which resulted in a fatality. We also filter for events which have occured over 10 times as there are numerous one-off EVTYPE classes that have a very high average number of fatalities.


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
# Across the United States, which types of events (as indicated in the EVTYPE variable) are most harmful with respect to population health?

fatal_storm <- filter(storm, FATALITIES > 0)
worst_events <- fatal_storm %>% 
        group_by(EVTYPE_comb) %>% 
        summarise(avg_deaths = mean(FATALITIES, na.rm = TRUE), 
                  max_deaths = max(FATALITIES, na.rm = TRUE),
                  total_deaths = sum(FATALITIES, na.rm = TRUE),
                  avg_inj = mean(INJURIES, na.rm = TRUE), 
                  max_inj = max(INJURIES, na.rm = TRUE),
                  total_inj = sum(INJURIES, na.rm = TRUE),
                  n = n()) %>%
        arrange(desc(avg_deaths)) %>% 
        filter(n > 10) %>% 
        slice(1:10)
worst_events
```

```
## # A tibble: 10 x 8
##             EVTYPE_comb avg_deaths max_deaths total_deaths    avg_inj
##                  <fctr>      <dbl>      <dbl>        <dbl>      <dbl>
##  1                 HEAT   3.947170        583         3138  8.1698113
##  2              TORNADO   3.515908        158         5636 37.5708047
##  3            LANDSLIDE   3.166667         14           38  2.2500000
##  4            HURRICANE   2.812500         15          135 25.7708333
##  5             WILDFIRE   2.368421         14           90 11.2368421
##  6       TROPICAL STORM   2.320000         22           58 10.9600000
##  7                  FOG   1.675676         11           62  8.3243243
##  8           HEAVY RAIN   1.580645         19           98  0.6612903
##  9         WINTER STORM   1.573892         14          639  8.7857143
## 10 HEAVY SURF/HIGH SURF   1.555556          5           42  0.3703704
## # ... with 3 more variables: max_inj <dbl>, total_inj <dbl>, n <int>
```


```r
library(reshape2)
library(ggplot2)
# melt data to enable a grid faceted plot for the different health outcomes
worst_melted <- melt(worst_events, id.vars = "EVTYPE_comb", variable.name = "health_outcome")
ggplot(data = worst_melted, aes(x = EVTYPE_comb, y = value)) + geom_col() + 
        facet_grid(health_outcome~., scales = "free") + 
        theme(axis.text.x = element_text(angle = 45, hjust = 1, vjust = 1),
              strip.text.y = element_text(angle = 0)) +
        ggtitle("Tornadoes Responsible for Most Deaths") +
        labs(xlab("Type of Storm Event"))
```

![](RepData_assignment2_files/figure-html/plot-worst-events-1.png)<!-- -->


```r
tornado_deaths <- filter(worst_events, EVTYPE_comb == "TORNADO")
tornado_deaths
```

```
## # A tibble: 1 x 8
##   EVTYPE_comb avg_deaths max_deaths total_deaths avg_inj max_inj total_inj
##        <fctr>      <dbl>      <dbl>        <dbl>   <dbl>   <dbl>     <dbl>
## 1     TORNADO   3.515908        158         5636 37.5708    1700     60226
## # ... with 1 more variables: n <int>
```

```r
tornado_death_perc <- tornado_deaths$total_deaths / sum(storm$FATALITIES)
tornado_death_perc <- round(tornado_death_perc * 100, 2)
tornado_death_perc <- paste(tornado_death_perc, "%")
```

Although heat related events had higher deaths on average the shear number of tornadoes made them responsible for the majority of deaths with 37.21 %. 

### Economic Consequences

View top events with respect to economic impact. We are interested in the PROPDMG and CROPDMG columns for determing an events economic effect. Both of these columns also have an accompanying modifier to signify thousands, millions etc. A new variable will be created to contain the actual dollar amount.


```r
summary(storm$PROPDMG)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.00    0.00    0.00   12.06    0.50 5000.00
```

```r
levels(storm$PROPDMGEXP)
```

```
##  [1] ""  "-" "?" "+" "0" "1" "2" "3" "4" "5" "6" "7" "8" "B" "h" "H" "K"
## [18] "m" "M"
```

```r
table(storm$PROPDMGEXP)
```

```
## 
##             -      ?      +      0      1      2      3      4      5 
## 465934      1      8      5    216     25     13      4      4     28 
##      6      7      8      B      h      H      K      m      M 
##      4      5      1     40      1      6 424665      7  11330
```

```r
table(storm$CROPDMGEXP)
```

```
## 
##             ?      0      2      B      k      K      m      M 
## 618413      7     19      1      9     21 281832      1   1994
```

```r
storm %>% 
        filter(PROPDMGEXP == "" & PROPDMG > 0) %>% 
        slice(1:20)
```

```
## # A tibble: 20 x 38
##    STATE__   BGN_DATE BGN_TIME TIME_ZONE COUNTY  COUNTYNAME  STATE
##      <dbl>     <dttm>   <fctr>    <fctr>  <dbl>      <fctr> <fctr>
##  1       6 1995-03-09     0301       PST     41       MARIN     CA
##  2      12 1993-01-08     1130       EST    125       UNION     FL
##  3      13 1995-08-19     1700       EST     87     DECATUR     GA
##  4      13 1993-03-31     2015       EST    147        HART     GA
##  5      18 1993-05-12     1630       EST     17        CASS     IN
##  6      18 1995-05-14     0300       EST    163 VANDERBURGH     IN
##  7      18 1995-05-18     0215       CST     51      GIBSON     IN
##  8      18 1995-05-17     2220       CST     83        KNOX     IN
##  9      18 1995-05-28     2220       EST    137      RIPLEY     IN
## 10      20 1995-05-12     2048       CST     41   DICKINSON     KS
## 11      20 1995-04-08     1845       CST     43    DONIPHAN     KS
## 12      20 1995-04-08     1845       CST     43    DONIPHAN     KS
## 13      20 1995-05-23     0730       CST    177     SHAWNEE     KS
## 14      21 1995-05-24     2040       CST     47   CHRISTIAN     KY
## 15      21 1995-05-18     1112       CST     85     GRAYSON     KY
## 16      26 1995-04-18     2025       EST     45       EATON     MI
## 17      26 1993-04-24     1855       EST     69       IOSCO     MI
## 18      28 1995-03-07     1010       CST     49       HINDS     MS
## 19      28 1995-03-07     1015       CST    127     SIMPSON     MS
## 20      36 1995-07-15     0630       EST     27    DUTCHESS     NY
## # ... with 31 more variables: EVTYPE <fctr>, BGN_RANGE <dbl>,
## #   BGN_AZI <fctr>, BGN_LOCATI <fctr>, END_DATE <fctr>, END_TIME <fctr>,
## #   COUNTY_END <dbl>, COUNTYENDN <lgl>, END_RANGE <dbl>, END_AZI <fctr>,
## #   END_LOCATI <fctr>, LENGTH <dbl>, WIDTH <dbl>, F <int>, MAG <dbl>,
## #   FATALITIES <dbl>, INJURIES <dbl>, PROPDMG <dbl>, PROPDMGEXP <fctr>,
## #   CROPDMG <dbl>, CROPDMGEXP <fctr>, WFO <fctr>, STATEOFFIC <fctr>,
## #   ZONENAMES <fctr>, LATITUDE <dbl>, LONGITUDE <dbl>, LATITUDE_E <dbl>,
## #   LONGITUDE_ <dbl>, REMARKS <fctr>, REFNUM <dbl>, EVTYPE_comb <fctr>
```

```r
# most observations with property damage do have modifiers

storm %>% 
        filter(CROPDMGEXP == "" & CROPDMG > 0) %>% 
        slice(1:20)
```

```
## # A tibble: 3 x 38
##   STATE__   BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME  STATE
##     <dbl>     <dttm>   <fctr>    <fctr>  <dbl>     <fctr> <fctr>
## 1      38 1994-07-04     0400       CST     93   STUTSMAN     ND
## 2      48 1994-04-05     1700       CST    209       HAYS     TX
## 3      48 1994-04-15     1630       CST    325     MEDINA     TX
## # ... with 31 more variables: EVTYPE <fctr>, BGN_RANGE <dbl>,
## #   BGN_AZI <fctr>, BGN_LOCATI <fctr>, END_DATE <fctr>, END_TIME <fctr>,
## #   COUNTY_END <dbl>, COUNTYENDN <lgl>, END_RANGE <dbl>, END_AZI <fctr>,
## #   END_LOCATI <fctr>, LENGTH <dbl>, WIDTH <dbl>, F <int>, MAG <dbl>,
## #   FATALITIES <dbl>, INJURIES <dbl>, PROPDMG <dbl>, PROPDMGEXP <fctr>,
## #   CROPDMG <dbl>, CROPDMGEXP <fctr>, WFO <fctr>, STATEOFFIC <fctr>,
## #   ZONENAMES <fctr>, LATITUDE <dbl>, LONGITUDE <dbl>, LATITUDE_E <dbl>,
## #   LONGITUDE_ <dbl>, REMARKS <fctr>, REFNUM <dbl>, EVTYPE_comb <fctr>
```

```r
# most observations with crop damage do have modifiers
```


```r
# since we are interested in damaging storms we will filter for those that caused damage
damage_storm <- filter(storm, PROPDMG > 0 | CROPDMG > 0)
# initialize new columns
damage_storm$prop.damage.dollars <- 0
damage_storm$crop.damage.dollars <- 0

# use for loop to calculate dollar values
for (i in 1:length(damage_storm$STATE__)) {
        
        if (damage_storm$PROPDMGEXP[i] %in% c("k", "K")) {
                damage_storm$prop.damage.dollars[i] <- 
                        damage_storm$PROPDMG[i] * 1000
        }
        else if (damage_storm$PROPDMGEXP[i] %in% c("m", "M")) {
                damage_storm$prop.damage.dollars[i] <- 
                        damage_storm$PROPDMG[i] * 1000000
        }
        else if (damage_storm$PROPDMGEXP[i] %in% c("b", "B")) {
                damage_storm$prop.damage.dollars[i] <- 
                        damage_storm$PROPDMG[i] * 1000000000
        }
        else {
                damage_storm$prop.damage.dollars[i] <- damage_storm$PROPDMG[i]
        }
}

for (i in 1:length(damage_storm$STATE__)) {
        
        if (damage_storm$CROPDMGEXP[i] %in% c("k", "K")) {
                damage_storm$crop.damage.dollars[i] <- 
                        damage_storm$CROPDMG[i] * 1000
        }
        else if (damage_storm$CROPDMGEXP[i] %in% c("m", "M")) {
                damage_storm$crop.damage.dollars[i] <- 
                        damage_storm$CROPDMG[i] * 1000000
        }
        else if (damage_storm$CROPDMGEXP[i] %in% c("b", "B")) {
                damage_storm$crop.damage.dollars[i] <- 
                        damage_storm$CROPDMG[i] * 1000000000
        }
        else {
                damage_storm$crop.damage.dollars[i] <- damage_storm$CROPDMG[i]
        }
}
```


```r
# create a new data frame with total damage and event type
total_damage <- damage_storm %>% 
        mutate(total.damage.dollars = prop.damage.dollars + crop.damage.dollars) %>% 
        select(EVTYPE_comb, total.damage.dollars)
```


```r
plot_damage <- total_damage %>% 
        group_by(EVTYPE_comb) %>% 
        summarise(total.damage = sum(total.damage.dollars), n = n()) %>% 
        arrange(desc(total.damage)) %>% 
        slice(1:10)

ggplot(data = plot_damage, aes(x = EVTYPE_comb, y = total.damage, fill = n)) +
        geom_col() + 
        theme(axis.text.x = element_text(angle = 45, hjust = 1, vjust = 1),
              strip.text.y = element_text(angle = 0)) +
        ggtitle("Flooding Responsible for Most Damage") +
        labs(xlab("Type of Storm Event"))
```

![](RepData_assignment2_files/figure-html/plot-damage-1.png)<!-- -->

Floods are responsible for the most damage overall, however there are many more floods than hurricanes. We will also compare average damage per event.


```r
# add average damage per events as a column
plot_damage <- mutate(plot_damage, avg.damage = total.damage / n)

ggplot(data = plot_damage, aes(x = EVTYPE_comb, y = avg.damage)) +
        geom_col() + 
        theme(axis.text.x = element_text(angle = 45, hjust = 1, vjust = 1),
              strip.text.y = element_text(angle = 0)) +
        ggtitle("Hurricanes Have Highest Average Damage") +
        labs(xlab("Type of Storm Event"))
```

![](RepData_assignment2_files/figure-html/plot-average-damage-1.png)<!-- -->


```r
avg_hurricane_dmg <- plot_damage[which(plot_damage$EVTYPE_comb == "HURRICANE"), 4]
avg_hurricane_dmg <- paste0("$", round(avg_hurricane_dmg / 1000000, 2), " Million")
```


When looking at average damage, hurricanes become the most significant storm event with $423.81 Million in damage per storm. Floods actaully do relatively little damage on average, so as with the health effects, it is the large number of them that makes them the most damaging event overall. 
