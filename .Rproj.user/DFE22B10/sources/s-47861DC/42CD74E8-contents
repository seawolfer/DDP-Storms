library(shiny)
library(ggplot2)

shinyUI(
  navbarPage("Damage to the US Economy per State Due to Weather Disasters",
             tabPanel("Selection and Results",
                      sidebarLayout(
                        sidebarPanel(
                          tags$style(HTML(".js-irs-0 .irs-single, .js-irs-0 .irs-bar-edge, .js-irs-0 .irs-bar {background: darkred}")),
                          sliderInput("period", 
                                      "Select a Period of Time:", 
                                      min = 1950,
                                      max = 2010,
                                      value = c(1950, 2010),
                                      sep=""
                          ),
                          radioButtons("damageCategory",
                                       
                                       "Select a Category of Economic Damage",
                                       c("Property and Harvest Damage" = "all", "Property Damage" = "property", "Harvest Damage" = "crop")
                          )
                        ),
                        mainPanel(
                          tabsetPanel(
                            tabPanel(p(icon("line-chart"), "Map of the Economic Damage per State"),
                                     column(7,
                                            includeMarkdown("Main_1.md"),
                                            plotOutput("economicDamageState",  width = "150%", height ="500px")
                                     )
                            ),
                            tabPanel(p(icon("table"), "Values of the Economic Damage per State"),
                                     column(10,
                                            includeMarkdown("Main_2.md"),
                                            tableOutput("view")
                                     )
                            )
                            
                          )
                          
                        )
                      )
             ),
             
             tabPanel("Instructions",
                      mainPanel(
                        includeMarkdown("Instruction.md")
                      )
             ),
             navbarMenu("More",
                        tabPanel("Previous studies",
                                 includeMarkdown("PreviousStudies.md")),
                        tabPanel("References",
                                 includeMarkdown("References.md"))
             )
  )
)

