library(shiny)
library(ggplot2)
library(Rmisc)
library(dplyr)
library(mapproj)
library(maps)
library(plotly)
library(markdown)

## optimize method for large dataset
# read the firt 10 lines 
# ignore the comments
# get the class of each variables
data_10rows<- read.table("./data/repdata_data_StormData.csv", 
                         header=TRUE, sep=",",nrows = 10)

firstup <- function(x) {
        substr(x, 1, 1) <- toupper(substr(x, 1, 1))
        x
}

# to have the correct class for some type of variables
classes<-sapply(data_10rows,class)
classes <- ifelse(classes =='integer', 'character', classes)
classes <- ifelse(classes == 'logical', 'character', classes)

## read the full dataset using the classes and ignore the comments
storm_database<- read.table("./data/repdata_data_StormData.csv",
                            header=TRUE, sep=",",
                            colClasses = classes, comment.char = "")

# select only the interesting variables
storm_database_subset <- storm_database[,c("EVTYPE", "FATALITIES", "INJURIES", "PROPDMG", 
                                           "PROPDMGEXP", "CROPDMG", "CROPDMGEXP","STATE","BGN_DATE")]

# transform all letter in capital letter
storm_database_subset$EVTYPE<-toupper(storm_database_subset$EVTYPE)
storm_database_subset$PROPDMGEXP<-toupper(storm_database_subset$PROPDMGEXP)
storm_database_subset$CROPDMGEXP<-toupper(storm_database_subset$CROPDMGEXP)

# change state abbreviations to state names (lowercase) to match the map data
storm_database_subset$STATE <- tolower(state.name[match(storm_database_subset$STATE, state.abb)])

# remove leading/trailing whitespaces
storm_database_subset$EVTYPE<-trimws(storm_database_subset$EVTYPE)

# remove data for which the variable `EVTYPE` contain `SUMMARY` since this is not a climatic event
storm_database_subset <- storm_database_subset %>% filter(!grepl("SUMMARY",storm_database_subset$EVTYPE))

# try to regroup similar group using different wording to get NOAA specified events
# storm
storm_database_subset$EVTYPE<-gsub("STORMS","STORM", storm_database_subset$EVTYPE)
# wind
storm_database_subset$EVTYPE<-gsub("TSTM", "THUNDERSTORM", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("^THUNDERSTORM$","THUNDERSTORM WIND", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("^TORNADOES, THUNDERSTORM WIND, HAIL$", "THUNDERSTORM WIND", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("^THUNDERSTORM WIND/HAIL$", "THUNDERSTORM WIND", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("^SEVERE THUNDERSTORM", "THUNDERSTORM WIND", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("WINDS", "WIND", storm_database_subset$EVTYPE)
# wild
storm_database_subset$EVTYPE<-gsub("WILD/FOREST ", "WILD", storm_database_subset$EVTYPE)
# flood
storm_database_subset$EVTYPE<-gsub("FLOOD/FLOOD$", "FLOOD", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("FLOOD & HEAVY RAIN$","FLOOD", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("FLOOD/RIVER FLOOD$","FLOOD", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("FLOODING$","FLOOD", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("URBAN/SML STREAM FLD","FLOOD", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("^RIVER FLOOD$","FLOOD", storm_database_subset$EVTYPE)
# flash flood
storm_database_subset$EVTYPE<-gsub("FLOOD/FLASH FLOOD$","FLASH FLOOD", storm_database_subset$EVTYPE)
# costal flood
storm_database_subset$EVTYPE<-gsub("COASTAL FLOODING$","COASTAL FLOOD", storm_database_subset$EVTYPE)
# excessive heat
storm_database_subset$EVTYPE<-gsub("EXTREME HEAT$", "EXCESSIVE HEAT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("HEAT WAVE$", "EXCESSIVE HEAT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("RECORD/HEAT$","EXCESSIVE HEAT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("RECORD HEAT$","EXCESSIVE HEAT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("RECORD/EXCESSIVE HEAT$","EXCESSIVE HEAT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("UNSEASONABLY WARM$","EXCESSIVE HEAT", storm_database_subset$EVTYPE)
# drought
storm_database_subset$EVTYPE<-gsub("HEAT DROUGHT$","DROUGHT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("DROUGHT/HEAT$","DROUGHT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("UNSEASONABLY WARM AND DRY$","DROUGHT", storm_database_subset$EVTYPE)
# rip current
storm_database_subset$EVTYPE<-gsub("CURRENTS", "CURRENT", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("RIP CURRENT/HEAVY SURF$","RIP CURRENT", storm_database_subset$EVTYPE)
# extreme cold
storm_database_subset$EVTYPE<-gsub("EXTREME COLD$", "EXTREME COLD/WIND CHILL", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("EXTREME WINDCHILL$", "EXTREME COLD/WIND CHILL", storm_database_subset$EVTYPE)
# hurricane
storm_database_subset$EVTYPE<-gsub("HURRICANE/TYPHOON$", "HURRICANE", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("HURRICANE OPAL","HURRICANE", storm_database_subset$EVTYPE)
# surf
storm_database_subset$EVTYPE<-gsub("HEAVY SURF/HIGH SURF$", "HIGH SURF", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("HEAVY SURF$", "HIGH SURF", storm_database_subset$EVTYPE)
# winter weather
storm_database_subset$EVTYPE<-gsub("WINTER WEATHER/MIX$","WINTER WEATHER", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("COLD AND SNOW$","WINTER WEATHER", storm_database_subset$EVTYPE)
# dense fog
storm_database_subset$EVTYPE<-gsub("^FOG$", "DENSE FOG", storm_database_subset$EVTYPE)
# cold
storm_database_subset$EVTYPE<-gsub("^COLD$", "COLD/WIND CHILL", storm_database_subset$EVTYPE)
# wind
storm_database_subset$EVTYPE<-gsub("^WIND$", "STRONG WIND", storm_database_subset$EVTYPE)
# strom surge/tide
storm_database_subset$EVTYPE<-gsub("^STORM SURGE$","STORM SURGE/TIDE", storm_database_subset$EVTYPE)
# avalance
storm_database_subset$EVTYPE<-gsub("^LANDSLIDE$","AVALANCHE", storm_database_subset$EVTYPE)
# frost
storm_database_subset$EVTYPE<-gsub("^GLAZE$","FROST/FREEZE", storm_database_subset$EVTYPE)
storm_database_subset$EVTYPE<-gsub("^ICE$","FROST/FREEZE", storm_database_subset$EVTYPE)
# tropical storm
storm_database_subset$EVTYPE<-gsub("^TROPICAL STORM GORDON","TROPICAL STORM", storm_database_subset$EVTYPE)
# heavy rain
storm_database_subset$EVTYPE<-gsub("HEAVY RAIN/SEVERE WEATHER","HEAVY RAIN", storm_database_subset$EVTYPE)

# replace symbol `0~9`, `-`, `+` and `?` by NA
index <- storm_database_subset$PROPDMGEXP %in% c("-", "?", "+", "0", "1", "2", "3", "4", "5", "6", "7", "8")
storm_database_subset$PROPDMGEXP[index] <- as.character(NA)

index <- storm_database_subset$CROPDMGEXP %in% c("-", "?", "+", "0", "1", "2", "3", "4", "5", "6", "7", "8")
storm_database_subset$CROPDMGEXP[index] <- as.character(NA)

# replace symbol by numeric multiplier
storm_database_subset$PROPDMGEXP[storm_database_subset$PROPDMGEXP %in% c("")] <- 10^0
storm_database_subset$PROPDMGEXP[storm_database_subset$PROPDMGEXP %in% c("H")] <- 10^2
storm_database_subset$PROPDMGEXP[storm_database_subset$PROPDMGEXP %in% c("K")] <- 10^3
storm_database_subset$PROPDMGEXP[storm_database_subset$PROPDMGEXP %in% c("M")] <- 10^6
storm_database_subset$PROPDMGEXP[storm_database_subset$PROPDMGEXP %in% c("B")] <- 10^9

# replace symbol by numeric multiplier
storm_database_subset$CROPDMGEXP[storm_database_subset$CROPDMGEXP %in% c("")]  <- 10^0
storm_database_subset$CROPDMGEXP[storm_database_subset$CROPDMGEXP %in% c("H")] <- 10^2
storm_database_subset$CROPDMGEXP[storm_database_subset$CROPDMGEXP %in% c("K")] <- 10^3
storm_database_subset$CROPDMGEXP[storm_database_subset$CROPDMGEXP %in% c("M")] <- 10^6
storm_database_subset$CROPDMGEXP[storm_database_subset$CROPDMGEXP %in% c("B")] <- 10^9

# add a new variable to store the property damage in dollars
storm_database_subset <- mutate(storm_database_subset, PROPDMGCOST = as.numeric(PROPDMG) * as.numeric(PROPDMGEXP))
storm_database_subset <- mutate(storm_database_subset, CROPDMGCOST = as.numeric(CROPDMG) * as.numeric(CROPDMGEXP))
storm_database_subset <- mutate(storm_database_subset, TOTDMGCOST  = PROPDMGCOST + CROPDMGCOST)

# add the year
storm_database_subset$BGN_DATE <- as.Date(storm_database_subset$BGN_DATE, "%m/%d/%Y")
storm_database_subset <- mutate(storm_database_subset, YEAR  = as.numeric(format(storm_database_subset$BGN_DATE,'%Y')))

# function to get the state per year
aggregateState <- function(storm_database, year_min, year_max) {
        
        # select data
        subset_date<-storm_database %>% filter(YEAR >= year_min, YEAR <= year_max)
        
        # calculate the property damage, the crop damage and the total damage in dollars during climate events
        storm_database_aggregate_damage<-aggregate(cbind(TOTDMGCOST,PROPDMGCOST,CROPDMGCOST)~STATE,
                                                   subset_date,sum)
        # sort them by descending order
        storm_database_aggregate_damage<-storm_database_aggregate_damage[with(storm_database_aggregate_damage,order(-TOTDMGCOST)),]
        
        # use billion of dollar
        storm_database_aggregate_damage$TOTDMGCOST<-storm_database_aggregate_damage$TOTDMGCOST/1e9
        storm_database_aggregate_damage$PROPDMGCOST<-storm_database_aggregate_damage$PROPDMGCOST/1e9
        storm_database_aggregate_damage$CROPDMGCOST<-storm_database_aggregate_damage$CROPDMGCOST/1e9
        
        storm_database_aggregate_damage<-storm_database_aggregate_damage[order(storm_database_aggregate_damage$STATE), ]
        
        return(storm_database_aggregate_damage)
}

# select damages
selectDamages <- function(dataframe, damageCategory) {
        dataframe %>% mutate(DAMAGES = {
                
                if(damageCategory == 'all') {
                        TOTDMGCOST
                } else if(damageCategory == 'crop') {
                        CROPDMGCOST
                } else if(damageCategory == 'property') {
                        PROPDMGCOST
                }
        })
}

# load maps
states_map <- map_data("state")

# create a map for the US
mapImpactState <- function (dataframe, states_map, year_min, year_max, fill, title) {
        
        firstup <- function(x) {
                substr(x, 1, 1) <- toupper(substr(x, 1, 1))
                x
        }
        
        # define a tittle
        title <- sprintf(title, year_min, year_max)
        # get the name of the region
        cnames <-aggregate(cbind(long, lat) ~ region, data = states_map, FUN = function(x) mean(range(x)))
        cnames$angle <-0
        # define the plot
        g <- ggplot(dataframe, aes(map_id = STATE))
        g <- g + geom_map(aes_string(fill = fill), map = states_map, colour='black')
        g <- g + expand_limits(x = states_map$long, y = states_map$lat)
        g <- g + theme_bw()
        g <- g + theme(legend.position = "bottom",
                       axis.ticks = element_blank(), 
                       axis.title = element_blank(), 
                       axis.text =  element_blank())
        g <- g + scale_fill_gradient(low="white", high="darkred", name="Damage to the US economy in (billion of USD)") 
        g <- g + guides(fill = guide_colorbar(barwidth = 10, barheight = .5)) 
        g <- g + geom_text(data=cnames, aes(long, lat, label = firstup(region),  
                                            angle=angle, map_id =NULL), size=3.5)
        
        #ggplotly(g) ## doesn't work
        g
}


shinyServer(
        function(input, output) {
                # Prepare dataset for maps
                storm_data <- reactive({
                        aggregateState(storm_database_subset, input$period[1], input$period[2])
                })
                
                output$economicDamageState <- renderPlot({
                        print(mapImpactState(
                                dataframe = selectDamages(storm_data(), input$damageCategory),
                                states_map = states_map, 
                                year_min = input$period[1],
                                year_max = input$period[2],
                                title = "Weather Disasters Events Representing Most of Damage to the Economy of \n the United States between %d - %d (Billion of USD)",
                                fill = "DAMAGES"
                        )
                        )
                })
                
                output$view <- renderTable({
                        head(storm_data(), n = nrow(storm_data()))}, colnames = FALSE)               
        }
)